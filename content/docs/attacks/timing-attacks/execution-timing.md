+++
title = "Execution Timing"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++

Measuring the time of JavaScript execution in a browser can give attackers information on when certain events are triggered, and how long some operations take. 

## Timing the Event Loop

JavaScript concurrency model is based on a [single-threaded event loop](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop) which means it can only run one task at a time. If, for example, some time-consuming task blocks the event loop, the user can perceive a freeze on a page as a result of the UI thread being starved. Other tasks must wait until the blocking one runs to conclusion. 

Some attacks exploit this model to steal secrets from a cross-origin page:

- When an attacker controls a string that is compared to a secret in a different origin, it might be possible to measure the comparison time of those strings [^2].
- An attacker might be able to infer how long code from a different origin takes to run, by measuring how long it takes to run next in the event pool [^1]. The attacker keeps sending events to the event loop with fixed properties, which will eventually be dispatched if the pool is empty. Other origins will dispatch events to the same pool, and this is where an attacker infers the timing difference by detecting if a delay occurred with one of its tasks.

{{< hint warning >}}
This attack is no longer possible in Browsers with process isolation mechanisms in place. Such mechanisms are only present in Chromium-Based browsers with [Site Isolation](https://www.chromium.org/Home/chromium-security/site-isolation) and *soon* in Firefox under [Project Fission](https://wiki.mozilla.org/Project_Fission).
{{< /hint >}}

## Service Workers

[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) can be abused by attackers to measure the timing of javascript execution in certain scenarios [^4]. They serve as a `proxy` between the browser and the network and allow applications to intercept any network requests made by the main thread (document). This feature is useful to offer offline solutions in web applications.

To make a timing measurement an attacker can perform the following steps:

1. The attacker registers a service worker in one of its domains (attacker.com).
2. In the main document, the attacker issues a navigation (window.open) to the target website and instructs the Service Worker to start a timer.
3. When the navigation starts loading the attacker will navigate the reference obtained in step 2 to a page handled by the service worker.
4. When the request performed in step 3 arrives to the Service Worker it will return a 204 (No Content) response, which will abort the navigation.
5. At this point the Service Worker will collect a measurement from the timer started in step 2. This measurement will be affected by how long JavaScript blocked the navigation for.

The navigation won't actually happen, but by timing how long the browser took to navigate to the Service Worker it's possible to time the page execution.

<!--TODO(manuelvsousa): This can also be used to detect a navigation. Maybe we should add it to the navigations article as well? -->

## CSS Injections

Certain XS-Leaks can be preformed if a CSS Injection is possible [^6]. Among the different CSS Injection vectors, the most noticeable one is abusing CSS Selectors. They can be used as an expression to match certain HTML elements. The selector `input[value^="a"]` will be matched if the value of an `input` tag with starts with the character "a". If a match occurs, attackers could then trigger a request to one of their websites using background, @import, etc to leak that occurrence. The matching process can be easily brute-forced, and extended to the full string.

The attacker is able to inject CSS and control the execution time with the following selector will spend more time running if a `main` tag with `id='site-main'` exists:

### jQuery, CSS Selectors & Short-circuit Timing

Attackers can abuse another interesting behavior of CSS selectors which is `short-circuit` evaluation of expressions. This expression is received in an `URL` hash and evaluated if the page uses (`jQuery(location.hash)`) [^3].

```javascript
$("*:has(*:has(*:has(*)) *:has(*:has(*:has(*))) *:has(*:has(*:has(*)))) main[id='site-main']")
```

A timing attack is possible because the expression is compared from right to left, so if the selector `main[id='site-main']` does not match and fails to evaluate, the other parts of the selector (`*:has(*:has(*:has(*))))`) which take longer to execute, are ignored (just like the `and` operator but backwards).

{{< hint warning >}}
This attack is no longer possible in Browsers with process isolation mechanisms in place. Such mechanisms are only present in Chromium-Based browsers with [Site Isolation](https://www.chromium.org/Home/chromium-security/site-isolation) and *soon* in Firefox under [Project Fission](https://wiki.mozilla.org/Project_Fission).
{{< /hint >}}

{{< hint info >}}
In browsers with process isolation mechanisms, [Service Workers]({{< ref "execution-timing.md#service-workers" >}}) can be abused to obtain the execution timing measurement or tricks like [Busy Event Loop tricks]({{< ref "#busy-event-loop" >}}) to circumvent Site Isolation.
{{< /hint >}}

## ReDoS

Regular Expression Denial of Service (ReDoS) it's an attack which result in a Denial of Service in applications that allow Regex as user input [^2] [^5]. The DoS results from an injected Regex that would run in exponential time. Some attacks applied this principle into leaking information: The attacker's injection cause a DoS if the Regex matches a character in some secret and computes quickly otherwise. This could happen in both client and server side.

### Busy Event Loop

Attackers can make the [event loop busy](https://gist.github.com/terjanq/60b4ae4ce7491a0f3104e62e2ab07c87#file-iframes-html-L11-L33) with a long computation Regex. This is a trick to circumvent Site Isolation as an attacker origin can mess with the execution of another website. The attack works as follows:

1. Navigate the target website in a separate window with `window.open` or inside an iframe (if [Framing Protections](https://TODO) are **not** in place).
2. Wait for the long computation to start.
3. Load the target website inside an iframe (regardless of any [Framing Protections](https://TODO)). An attacker can detect if step 1 is still computing by checking if the iframe started loading (onload). Since both navigations occurred within same-site, they run in the same thread and share the same event loop (they can block each other), as Site Isolation is not enforced.

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| T. Event Loop       |         ✔️         |      ✔️         |  ❌   |          ❌         |
| Service Workers     |         ✔️         |      ✔️         |  ❌   |          ❌         |
| jQuery              |         ✔️         |      ✔️         |  ❌   |          ❌         |
| ReDoS               |         ✔️         |      ✔️         |  ❌   |          ❌         |
| Busy Event Loop     |         ✔️         |      ✔️         |  ❌   |          ✔️         |

[^1]: Loophole: Timing Attacks on Shared Event Loops in Chrome, [link](https://www.usenix.org/system/files/conference/usenixsecurity17/sec17-vila.pdf)
[^2]: Matryoshka - Web Application Timing Attacks (or.. Timing Attacks against JavaScript Applications in Browsers), [link](https://sirdarckcat.blogspot.com/2014/05/matryoshka-web-application-timing.html)
[^3]: A timing attack with CSS selectors and Javascript, [link](https://blog.sheddow.xyz/css-timing-attack/)
[^4]: Security: XS-Search + XSS Auditor = Not Cool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=922829)
[^5]: A Rough Idea of Blind Regular Expression Injection Attack, [link](https://diary.shift-js.info/blind-regular-expression-injection/)
[^6]: CSS Injection Primitives, [link](https://x-c3ll.github.io/posts/CSS-Injection-Primitives/)
