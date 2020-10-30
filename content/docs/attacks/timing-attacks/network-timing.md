+++
title = "Network Timing"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP",
    "Framing Protections",
]
menu = "main"
weight = 2
+++

Network Timing side-channels have been present on the web since its beginning [^1] [^4]. These attacks have had different levels of impact over time, gaining new attention when browsers started shipping high precision timers like [performance.now()]({{< ref "clocks.md#performancenow" >}}).

To obtain timing measurements attackers must use a [clock]({{< ref "clocks.md" >}}), either an implicit or explicit one. These clocks are usually interchangeable for the purposes of XS-Leaks and only vary in accuracy and availability. For simplicity, this article will assume use of the `performance.now()` API, an explicit clock present in all modern browsers.

This side-channel allows attackers to infer information from a cross-site request based on how much time it takes to complete that request [^2]. The network timing measurement may vary based on a user state and it's usually connected to:

- The resource size.
- The computation time in the backend.
- The number and size of sub-resources.
- [Cache status]({{< ref "../cache-probing.md" >}}).

{{< hint good >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "clocks.md" >}}).
{{< /hint >}}

## Modern Web Timing Attacks

The [performance.now()]({{< ref "clocks.md#performancenow" >}}) API can be used to measure how much time it takes to perform a request.

```javascript
var start = performance.now()
fetch('https://example.org', {
  mode: 'no-cors',
  credentials: 'include'
}).then(() => {
  var time = performance.now() - start;
  console.log("The request took %dms.", time);
});
```

## Onload events

A similar process can be used to measure how long it takes to fetch a resource by simply watching for an onload event.

```javascript
var script = document.createElement('script');
script.src = "https://example.org";
document.body.appendChild(script);
var start = performance.now();
script.onload = () => {
  var time = performance.now() - start;
  console.log("The request took %dms.", time)
}
```

{{< hint good >}}
A similar process can be repeated for other HTML elements, e.g. `<img>`, `<link>`, `<iframe>` which could be used in scenarios where other techniques fail. E.g. [Fetch Metadata]({{< ref "/docs/defenses/opt-in/fetch-metadata.md">}}) protections
{{< /hint >}}

## Sandboxed Frame Timing Attacks

If a page doesn't have any [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) implemented, an attacker can obtain the time measurement of both the initial request and subresources load. Because the loading time for the iframe depends on both the network and processing the subresources (e.g. expensive scripts), the attacker can eliminate the noise of script execution by including the [`sandbox`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) attribute in the `<iframe>`. This attribute will block a lot of features, including script execution if `allow-scripts` value is not specified.

```javascript
var iframe = document.createElement('iframe');
// Set the URL of the destination website
iframe.src = "https://example.org";
// Set sandbox attribute to block script execution
iframe.sandbox = "";
document.body.appendChild(iframe);

// Measure the time before the request was initiated
var start = performance.now();

iframe.onload = () => {
  // When iframe loads, calculate the time difference
  var time = performance.now() - start;
  console.log("The iframe and subresources took %dms to load.", time)
}
```

## Cross-window Timing Attacks

An attacker can also measure the network timing of a page by opening a new window with `window.open` and waiting for the `window` to start loading. The snippet below shows how to make this measurement.

```javascript
// Open a new window to measure when the iframe starts loading
var win = window.open('https://example.org');
// Measure the initial time
var start = performance.now();
// Define the loop
function measure(){
  try{
    // If the window is still same-origin, immediately repeat the loop
    // but without blocking the event loop
    win.origin;
    setTimeout(measure, 0);
  }catch(e){
    // When the window stwitches origins, calculate the time difference
    var time = performance.now() - start;
    console.log('It took %dms to load the window', time);
  }
}
// Initiate the loop that breaks when the window switches origins
measure();
```

{{< hint good >}}
This technique can also be adapted to measure the Execution Timing of a page by [making the event loop busy]({{< ref "execution-timing.md#busy-event-loop" >}}).
{{< /hint >}}

## Timeless Timing Attacks

Other attacks do not consider the notion of time to perform a timing attack [^3]. Timeless attacks consist of fitting two `HTTP` requests in a single packet, the baseline and the attacked request, to guarantee they arrive at the same time to the server. The server *will* process the requests concurrently, and return a response based on their execution time as soon as possible. One of the two requests will arrive first, allowing the attacker to get the timing difference by comparing the order in which the requests arrived.

The advantage of this technique is the independence on network jitter and uncertain delays, something that is always present in the remaining techniques.

{{< hint warning >}}
The original research needs to be adapted to work in a browser since it handles all network-specific tasks.
{{< /hint >}}

{{< hint warning >}}
This attack is limited to specific versions of HTTP and joint scenarios. It makes certain assumptions and has requirements regarding server behaviors.
{{< /hint >}}

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| Modern Timing Attacks              |         ✔️         |      ✔️         |  ❌   |          ❌         |
| Frame Timing (Network) |         ✔️       |      ✔️         |  ❌   |          -
| Frame Timing (Sandbox) |         ✔️       |      ✔️         |  ❌   |          -
| Cross-window Timing  |         ✔️  [(if Strict)]({{< ref "../../defenses/opt-in/same-site-cookies.md#lax-vs-strict" >}})      |      ✔️         |  ❌   |          ❌         |
| Timeless Timing  |         ✔️        |      ❓         |  ❌   |          ❌         |

## References

[^1]: Exposing Private Information by Timing Web Applications, [link](https://crypto.stanford.edu/~dabo/papers/webtiming.pdf)
[^2]: The Clock is Still Ticking: Timing Attacks in the Modern Web - Section 4.3.3, [link](https://tom.vg/papers/timing-attacks_ccs2015.pdf)
[^3]: Timeless Timing Attacks: Exploiting Concurrency to Leak Secrets over Remote Connections, [link](https://www.usenix.org/system/files/sec20-van_goethem.pdf)
[^4]: Cross-domain search timing, [link](https://scarybeastsecurity.blogspot.com/2009/12/cross-domain-search-timing.html)
