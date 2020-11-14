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

{{< hint tip >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "clocks.md" >}}).
{{< /hint >}}
## Modern Web Timing Attacks

The [performance.now()]({{< ref "clocks.md#performancenow" >}}) API can be used to measure how much time it takes to perform a request.

```javascript
// Start the clock
var start = performance.now()

// Measure how long it takes to complete the fetch requests
fetch('https://example.org', {
  mode: 'no-cors',
  credentials: 'include'
}).then(() => {
  // When fetch finishes, calculate the difference
  var time = performance.now() - start;
  console.log("The request took %d ms.", time);
});
```
## Onload events

A similar process can be used to measure how long it takes to fetch a resource by simply watching for an onload event.

```javascript
// Create a script element pointing to the page we want to time
var script = document.createElement('script');
script.src = "https://example.org";
document.body.appendChild(script);

// Start the clock
var start = performance.now();

// When script loads, caculate the time it took to finish the request
script.onload = () => {
  var time = performance.now() - start;
  console.log("The request took %d ms.", time)
}
```
{{< hint tip >}}
A similar technique can be used for other HTML elements, e.g. `<img>`, `<link>`, `<iframe>` which could be used in scenarios where other techniques fail. For example, if [Fetch Metadata]({{< ref "/docs/defenses/opt-in/fetch-metadata.md">}}) blocked loading of a resource into a script tag it may allow it into an image tag.
{{< /hint >}}

## Sandboxed Frame Timing Attacks

If a page doesn't have any [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) implemented, an attacker can time how long it takes for the page and all subresources to load over the network. By default, the `onload` handler for an iframe will be invoked after all the resources have been loaded and all Javascript has finished executing. But, an attacker can eliminate the noise of script execution by including the [`sandbox`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) attribute in the `<iframe>`. This attribute will block a lot of features including Javascript execution, which results in almost pure network measurement.

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
  console.log("The iframe and subresources took %d ms to load.", time)
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
    // If the page has loaded, then it will be on a different origin
    // so `win.origin` will throw an exception
    win.origin;
    // If the window is still same-origin, immediately repeat the loop but
    // without blocking the event loop
    setTimeout(measure, 0);
  }catch(e){
    // Once the window has loaded, calculate the time difference
    var time = performance.now() - start;
    console.log('It took %d ms to load the window', time);
  }
}
// Initiate the loop that breaks when the window switches origins
measure();
```
{{< hint note >}}
Note that this POC uses `setTimeout` in order to create the rough equivalent of a `while(true)` loop. It is necessary to implement this way in order to avoid blocking the JS event loop.
{{< /hint >}}
{{< hint tip >}}
This technique can also be adapted to measure the Execution Timing of a page by [making the event loop busy]({{< ref "execution-timing.md#busy-event-loop" >}}).
{{< /hint >}}

## Network timing using the unload events with a SharedArrayBuffer
The [SharedArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) can be used to create a high-resolution timer [link]({{< ref "clocks.md#sharedarraybuffer-and-web-workers">}}) however due to security conserns you need to set the following server headers:  
```
Cross-Origin-Embedder-Policy: "require-corp"  
Cross-Origin-Opener-Policy: "same-origin"  
```

To enable cross origin isolation so there will be restrictions for cross origin requests.  
However the timing diffrence between the beforeunload and unload event of iframes can still be used.
```javascript
iframe.contentWindow.onbeforeunload = _ => {
  // Get time during navigation
  start = Atomics.load(sharedArray, 0);
}
iframe.contentWindow.onunload = _ => {
  // Get time after navigation
  let time = Atomics.load(sharedArray, 0);
  console.log(time - start);
};
```
## Timeless Timing Attacks

Other attacks do not consider the notion of time to perform a timing attack [^3]. Timeless attacks consist of fitting two `HTTP` requests in a single packet, the baseline and the attacked request, to guarantee they arrive at the same time to the server. The server *will* process the requests concurrently, and return a response based on their execution time as soon as possible. One of the two requests will arrive first, allowing the attacker to get the timing difference by comparing the order in which the requests arrived.

The advantage of this technique is the independence on network jitter and uncertain delays, something that is always present in the remaining techniques.

{{< hint note >}}
The original research needs to be adapted to work in a browser since it handles all network-specific tasks.
{{< /hint >}}

{{< hint important >}}
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
