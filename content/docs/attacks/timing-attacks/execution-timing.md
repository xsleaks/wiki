+++
title = "Execution Timing"
description = ""
date = "2020-10-01"
category = "Attack"
abuse = [
    "Event Loop",
    "Service Workers",
    "Site Isolation",
    "CSS Injections",
    "Regex Injections",
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP",
    "Framing Protections",
]
menu = "main"
weight = 3
+++

Measuring the time of JavaScript execution in a browser can give attackers information on when certain events are triggered, and how long some operations take.

## Timing the Event Loop

JavaScript's concurrency model is based on a [single-threaded event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop) which means it can only run one task at a time. If, for example, some time-consuming task blocks the event loop, the user can perceive a freeze on a page as a result of the UI thread being starved. Other tasks must wait until the blocking task finishes. Each browser implements different [process models](https://chromium.googlesource.com/chromium/src/+/main/docs/process_model_and_site_isolation.md), which means some web sites might run in different threads (and event loops) depending on their relations.

Some techniques can exploit this model to steal secrets from a cross-origin page:

- Inferring how long code from a different origin takes to run by measuring how long it takes to run next in the event pool [^1] [^2]. The attacker keeps sending events to the event loop with fixed properties, which will eventually be dispatched if the pool is empty. Other origins dispatch events to the same pool, and this is where an attacker infers the time difference by detecting if a delay occurred with one of its tasks.
- Stealing a secret from a cross-origin page if the said secret is being compared by an attacker-controlled string. The leak is a result of comparing time differences in the event loop of a char-by-char string comparison [^2] (using the previous technique). In browsers without [process isolation](https://www.chromium.org/Home/chromium-security/site-isolation), cross-window communications between different origins run in the same thread, thus sharing the same event loop.

{{< hint important >}}
The latter attack is no longer possible in browsers with process isolation mechanisms in place. Such mechanisms are currently only present in Chromium-based browsers with [Site Isolation](https://www.chromium.org/Home/chromium-security/site-isolation); they are coming to Firefox *soon* under the name [Project Fission](https://wiki.mozilla.org/Project_Fission).
{{< /hint >}}

## Busy Event Loop

Another technique used to measure JavaScript execution consists of blocking the event loop of a thread and timing how long it takes for the event loop to become available again. One of the main advantages of this technique is its ability to circumvent Site Isolation, as an attacker origin can influence the execution of another origin. The attack works as follows:

1. Navigate the target website in a separate window with `window.open` or inside an `iframe` (if [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) are not in place).
2. Wait for the long computation to start.
3. Load any same-site page inside an `iframe`, regardless of any [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}).

An attacker can detect how long the target website is executed by timing how long it took for the `iframe` (in step 3) to trigger the `onload` event ([Network Timing]({{< ref "network-timing.md" >}}) of step 3 should be minimal). Since both navigations occurred within the same context and they are same-site, they run in the same thread and share the same event loop (they can block each other).

```javascript
// Open a new window to measure how long the window blocks the event loop
// for the site example.org
window.open('https://example.org/expensive');

// TODO: Wait for the expensive window to load, e.g. via timeout
// then create an iframe to the same site
var ifr = document.createElement('iframe');
ifr.src = "https://example.org";
document.body.appendChild(ifr);

// Measure the initial time
var start = performance.now();

ifr.onload = () => {
    // When the iframe loads calculate the time difference
    var time = performance.now() - start;
    console.log('It took %d ms to load the window', time);
}
```

## Service Workers

[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) can be used to offer offline solutions to web applications, but they can be abused by attackers to measure the timing of JavaScript execution[^4]. They serve as a proxy between the browser and the network and allow applications to intercept any network requests made by the main thread (document).

To make a timing measurement, an attacker can perform the following steps:

1. The attacker registers a service worker in one of their domains (attacker.com).
2. In the main document, the attacker issues a navigation (window.open) to the target website and instructs the Service Worker to start a timer.
3. When the new window starts loading, the attacker navigates the reference obtained in step 2 to a page handled by the Service Worker.
4. When the request performed in step 3 arrives at the service worker, it returns a 204 (No Content) response, which aborts the navigation.
5. At this point, the Service Worker collects a measurement from the timer started in step 2. This measurement is affected by how long JavaScript blocked the navigation.

Since no navigation actually occurs, steps 3 to 5 can be repeated to obtain more measurements on successive JavaScript execution timings.

### jQuery, CSS Selectors & Short-circuit Timing

Attackers can abuse another interesting behavior of CSS selectors which is `short-circuit` evaluation of expressions. This expression is received in a `URL` hash and evaluated if the page executes `jQuery(location.hash)` [^3].

A timing attack is possible because the expression is compared from right to left, so if the selector `main[id='site-main']` does not match and fails to evaluate, the other parts of the selector (`*:has(*:has(*:has(*))))`), which take longer to execute, are ignored (just like the `and` operator, but backwards).

```javascript
$("*:has(*:has(*:has(*)) *:has(*:has(*:has(*))) *:has(*:has(*:has(*)))) main[id='site-main']")
```

{{< hint tip >}}
In browsers with process isolation mechanisms, [Service Workers]({{< ref "execution-timing.md#service-workers" >}}) can be abused to obtain the execution timing measurement or tricks like [Busy Event Loop tricks]({{< ref "#busy-event-loop" >}}) can be used to circumvent Site Isolation.
{{< /hint >}}

## ReDoS

{{< hint warning >}}
This group of XS-Leaks requires an injection of Regex Expressions on the target page.
{{< /hint >}}

Regular Expression Denial of Service (ReDoS) is a technique which results in a Denial of Service in applications that allow regex as user input [^2] [^5]. Maliciously crafted regular expressions can be made to run in exponential time. This can be used as an XS-Leak vector if a regex can be injected that has a different runtime depending on the data on the page. This could happen on the client-side or the server-side.


## Defense

| Attack Alternative | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |    [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})    |
| :----------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-------------------------------------------------------------------------: |
|   T. Event Loop    |                                         ❌                                          |                          ❓                          |                                 ❌                                 | [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
|  Service Workers   |                                         ✔️                                          |                          ✔️                          |                                 ❌                                 | [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
|       jQuery       |                                         ✔️                                          |                          ❌                          |                                 ❌                                 | [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
|       ReDoS        |                                         ✔️                                          |                          ❌                          |                                 ❌                                 | [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |
|  Busy Event Loop   |                                         ✔️                                          |                          ✔️                          |                                 ❌                                 | [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |


## References

[^1]: Loophole: Timing Attacks on Shared Event Loops in Chrome, [link](https://www.usenix.org/system/files/conference/usenixsecurity17/sec17-vila.pdf)
[^2]: Matryoshka - Web Application Timing Attacks (or.. Timing Attacks against JavaScript Applications in Browsers), [link](https://sirdarckcat.blogspot.com/2014/05/matryoshka-web-application-timing.html)
[^3]: A timing attack with CSS selectors and Javascript, [link](https://blog.sheddow.xyz/css-timing-attack/)
[^4]: Security: XS-Search + XSS Auditor = Not Cool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=922829)
[^5]: A Rough Idea of Blind Regular Expression Injection Attack, [link](https://diary.shift-js.info/blind-regular-expression-injection/)
