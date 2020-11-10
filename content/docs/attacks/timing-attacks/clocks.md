+++
title = "Clocks"
description = ""
date = "2020-10-01"
category = "Instrument"
menu = "main"
weight = 1
+++

We can distinguish two types of clocks - Explicit and Implicit. Explicit clocks are the ones developers use to get direct timing measurements and such mechanisms are offered explicitly by the browser. Implicit clocks on the other hand, utilise particular web features to create unintended clocks that allow measuring the relative passage of time.


## Explicit Clocks

### performance.now API

The [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) API allows developers to get high-resolution timing measurements.

{{< hint info >}}
In order to mitigate some XS-Leaks, `performance.now()`'s accuracy was reduced from a range of nanoseconds to a microsecond precision in all modern browsers [^1] [^2] [^3].
<!-- TODO: "to mitigate some" means Size XS-Leaks that were fixed -->

[^1]: Reduce resolution of performance.now (Webkit). [link](https://bugs.webkit.org/show_bug.cgi?id=146531)
[^2]: Reduce precision of performance.now() to 20us (Gecko). [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1427870)
[^3]: Reduce resolution of performance.now to prevent timing attacks (Blink). [link](https://bugs.chromium.org/p/chromium/issues/detail?id=506723)
{{< /hint >}}

### Date API

The [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) API is the oldest API present in browsers to obtain timing measurements. It allows developers to get dates, and get Unix timestamps with `Date.now()`. These measurements are much less precise than [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now). Before the introduction of newer APIs attacks used to leverage this instead [^3].


## Implicit Clocks

### SharedArrayBuffer and Web Workers

With the introduction of `Web Workers`, new mechanisms to exchange data between threads were created [^1]. `SharedArrayBuffer`, one of those mechanisms, provides memory sharing between the main thread and a worker thread. Attackers can create an implicit clock by using two workers and a shared buffer. One worker runs in an infinite loop incrementing a number in the buffer. The other work can observe this number get incremented and use this to measure the relative passage of time.

```javascript
// -------- Main Thread clock creation --------
var buffer = new SharedArrayBuffer(16);
var counter = new Worker("counter.js");
counter.postMessage([buffer],[buffer]);
var arr = new UintArray(buffer);
relative_time = arr[0];

// -------- Web Worker counter.js --------
self.onmessage = function(event){
  var[buffer] = event.data ;
  var arr = newUintArray(buffer);
  while(1){
    arr[0]++;
  }
}

```
{{< hint important >}}
`SharedArrayBuffer` was removed from browsers with the publication of [Spectre](https://spectreattack.com/). It was reintroduced later in 2020 requiring documents to be in a [secure context](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) to make use of the API. Since secure contexts cannot reference any cross-origin content that has not explicitly opted in to being accessed, this means SharedArrayBuffers cannot be used as clocks for XS-Leaks.
{{< /hint >}}

{{< hint info >}}
Since Firefox 79, this API can be used with [full resolution](https://developer.mozilla.org/en-US/docs/Mozilla/Firefox/Releases/79) in documents which do not share a browsing context group with cross-origin documents. This will require an application interested in this precision to explicitly opt-in to [COOP]({{< ref "../../defenses/opt-in/coop.md" >}}) and [COEP](https://web.dev/coop-coep/).
{{< /hint >}}

### Other Clocks

There are a considerable number of APIs attackers can abuse to create implicit clocks: [Broadcast Channel API](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API), [Message Channel API](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel), [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame), [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout), CSS animations and others [^2] [^4].

## References

[^1]: Shared memory: Side-channel information leaks, [link](https://github.com/tc39/ecmascript_sharedmem/blob/master/issues/TimingAttack.md)
[^2]: Fantastic Timers and Where to Find Them: High-Resolution Microarchitectural Attacks in JavaScript, [link](https://gruss.cc/files/fantastictimers.pdf)
[^3]: Exposing Private Information by Timing Web Applications, [link](http://crypto.stanford.edu/~dabo/papers/webtiming.pdf)
[^4]: Trusted Browsers for Uncertain Times, [link](https://www.usenix.org/system/files/conference/usenixsecurity16/sec16_paper_kohlbrenner.pdf)
