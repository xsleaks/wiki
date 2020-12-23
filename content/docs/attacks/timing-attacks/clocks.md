+++
title = "Clocks"
description = ""
date = "2020-10-01"
category = "Instrument"
menu = "main"
weight = 1
+++

We can distinguish two types of clocks – explicit and implicit. Explicit clocks are used by developers to get direct timing measurements, mechanisms of this type are offered explicitly by the browser. In contrast, implicit clocks utilize particular web features to create unintended clocks that allow measuring the relative passage of time.


## Explicit Clocks

### performance.now API

The [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) API allows developers to get high-resolution timing measurements.

{{< hint info >}}
In order to mitigate some types of XS-Leaks, `performance.now()`'s accuracy was reduced from a range of nanoseconds to microsecond precision in all modern browsers [^1] [^2] [^3].
<!-- TODO: "to mitigate some" means Size XS-Leaks that were fixed -->

[^1]: Reduce resolution of performance.now (Webkit). [link](https://bugs.webkit.org/show_bug.cgi?id=146531)
[^2]: Reduce precision of performance.now() to 20us (Gecko). [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1427870)
[^3]: Reduce resolution of performance.now to prevent timing attacks (Blink). [link](https://bugs.chromium.org/p/chromium/issues/detail?id=506723)
{{< /hint >}}

### Date API

The [Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) API is the oldest API present in browsers which can be used to obtain timing measurements. It allows developers to get dates, and get Unix timestamps with `Date.now()`. These measurements are much less precise than [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now). Before the introduction of newer APIs, attacks used to leverage this API [^3].


## Implicit Clocks

### SharedArrayBuffer and Web Workers

With the introduction of `Web Workers`, new mechanisms to exchange data between threads were created [^1]. One of those mechanisms is `SharedArrayBuffer` which provides memory sharing between the main thread and a worker thread. A malicious website can create an implicit clock by loading a worker running an infinite loop that increments a number in the buffer. This value can then be accessed by the main thread at any time to read how many incrementations were performed.

{{< hint info >}}
`SharedArrayBuffer` was removed from browsers with the publication of [Spectre](https://spectreattack.com/). It was reintroduced later in 2020, requiring documents to be in a [secure context](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) to make use of the API. Since secure contexts cannot reference any cross-origin content that has not explicitly opted in to being accessed, this means SharedArrayBuffers cannot be used as clocks for some XS-Leaks.

To make use of `SharedArrayBuffer` in modern browsers, an application needs to explicitly opt in to [COOP]({{< ref "../../defenses/opt-in/coop.md" >}}) and [COEP](https://web.dev/coop-coep/) by setting the following headers:
```http
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Embedder-Policy: require-corp
```
{{< /hint >}}


```javascript
// Define a function to be ran inside a WebWorker
function worker_function() {
  self.onmessage = function (event) {
    const sharedBuffer = event.data;
    const sharedArray = new Uint32Array(sharedBuffer);

    // Infinitely increase the uint32 number
    while (true) Atomics.add(sharedArray, 0, 1);
  };
}

// Create the WebWorker from the JS function and invoke it
const worker = new Worker(
  URL.createObjectURL(
    new Blob([`(${worker_function})()`], {
      type: "text/javascript"
    }))
);

// Create a Shared buffer between the WebWorker and a document
const sharedBuffer = new SharedArrayBuffer(Uint32Array.BYTES_PER_ELEMENT);
const sharedArray = new Uint32Array(sharedBuffer);
worker.postMessage(sharedBuffer);
```

{{< hint tip >}}
To get the relative time in a main thread, you can use the [Atomics API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics).
```javascript
Atomics.load(sharedArray, 0);
```

{{< /hint >}}


### Other Clocks

There are a considerable number of APIs attackers can abuse to create implicit clocks: [Broadcast Channel API](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API), [Message Channel API](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel), [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame), [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout), CSS animations, and others [^2] [^4].

## References

[^1]: Shared memory: Side-channel information leaks, [link](https://github.com/tc39/ecmascript_sharedmem/blob/master/issues/TimingAttack.md)
[^2]: Fantastic Timers and Where to Find Them: High-Resolution Microarchitectural Attacks in JavaScript, [link](https://gruss.cc/files/fantastictimers.pdf)
[^3]: Exposing Private Information by Timing Web Applications, [link](http://crypto.stanford.edu/~dabo/papers/webtiming.pdf)
[^4]: Trusted Browsers for Uncertain Times, [link](https://www.usenix.org/system/files/conference/usenixsecurity16/sec16_paper_kohlbrenner.pdf)
