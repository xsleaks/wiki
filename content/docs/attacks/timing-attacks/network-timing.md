+++
title = "Network Timing"
description = ""
date = "2020-10-01"
category = [
    "Attack",
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

Network Timing side-channels have been present on the web since its beginning [^1] [^4]. These attacks achieved different levels of impact over time, gaining new attention when browsers started shipping high precision timers like [performance.now()]({{< ref "clocks.md#performancenow" >}}).

To obtain timing measurements attackers must use a [clock]({{< ref "clocks.md" >}}), either an implicit or explicit one. These clocks are usually interchangeable and only vary in accuracy and availability. For simplicity, this article will only address the `performance.now()` API, an explicit clock present in all modern browsers.

This side-channel allows attackers to infer information from a cross-site request based on how much time it takes to complete that request [^2]. The network timing measurement may vary based on a user state and it's usually connected to:

- Resource Size.
- The computation time in the backend.
- Amount of sub-resources.
- [Cache status]({{< ref "../cache-probing.md" >}}).

{{< hint good >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "clocks.md" >}}).
{{< /hint >}}

## Modern Web Timing Attacks

The [performance.now()]({{< ref "clocks.md#performancenow" >}}) API can be used to measure how much time it takes to perform a request.

```javascript
let before = performance.now()
await fetch('https://target-website.com',{'mode':'no-cors','credentials':'include'})
let request_time = performance.now() - before
```

## Frame Timing Attacks (Network)

If the target page enforces [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}), embedding it as an `iframe` allows an attacker to obtain a network timing measurement. The example below shows how to achieve this by starting a [clock]({{< ref "clocks.md" >}}), embedding the page as an `iframe` (request is started), and wait for the `onload` event to be triggered which means the request completed. In this scenario when the request completes the browser does not render the fetched resource because of the protection.

```javascript
begin = performance.now();
var x = document.createElement('iframe');
x.src = "https://target.page";
document.body.appendChild(x);
start = performance.now();
x.onload = () => console.log(performance.now() - begin)
```

## Sandboxed Frame Timing Attacks

When a page sets [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}), an attacker can obtain an almost pure network measurement by including the [`sandbox`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/iframe) attribute in the `iframe`. This attribute will block all JavaScript execution and prevent some subresources from loading.


## Cross-window Timing Attacks

An attacker can also measure the network timing of a page by opening a new window with `window.open` and wait for the `window` to start loading. The snippet below shows how to make this measurement and works as follows:

1. The attacker creates an infinite loop of `postMessage` broadcasts to itself while opening a window to the target website (lines 15, 2, 7). The clock is started (line 14).
2. When the window is created, its location [will be `about:blank`](https://developer.mozilla.org/en-US/docs/Web/API/Window/open) until the target page starts loading.
3. In the infinite loop, the logic clause (line 4) will be `true` while the opened window remains in `about:blank` as its document it's still accessible from the attacker's origin.
4. When the window starts loading, the location will change from `about:blank` to the target's origin.
5. The attacker's origin won't have access to the document of the opened window as per Same-Origin Policy. When this occurs, the logic clause (line 4) will fail and throw a `DOMException`.
6. The attacker catches the Exception and stops the clock.

{{< highlight javascript "linenos=table,linenostart=1" >}}
let w = 0, end = 0, begin = 0;
onmessage=()=>{
  try{
    if(w && w.document.cookie){
      // still same origin
    }
    postMessage('','*');
  }catch(e){
    end = performance.now();
    console.log('time to load was', end - begin);
  }
};
postMessage('','*');
begin = performance.now();
w = open('//mail.com/search?q=foo');
{{< / highlight >}}

{{< hint good >}}
This technique can also be adapted to measure the Execution Timing of a page by [making the event loop busy]({{< ref "execution-timing.md#busy-event-loop" >}}).
{{< /hint >}}

## Timeless Timing Attacks

Other attacks do not consider the notion of time to perform a timing attack [^3]. Timeless attacks consist of fitting two `HTTP` requests in a single packet, the baseline and the attacked request, to guarantee they arrive at the same time to the server. The server *will* process the requests concurrently, and return a response based on their execution time as soon as possible. One of the two requests will arrive first, allowing the attacker to get the timing difference by comparing both requests.

The advantage of this technique is the independence on network jitter and uncertain delays, something that is **always** present in the remaining techniques.

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
