+++
title = "Clocks"
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

There are two types of clocks: Implicit and Explicit clocks. Explicit clocks are the ones developers use to get direct timing measurements and such mechanisms are offered **explicitly** by the browser.  Implicit clocks on the other hand, misuse particular web features to create unintended clocks which offer a relative timing.


## Explicit Clocks

### performance.now

The [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) API allows developers to get high-resolution timing measurements.

{{< hint info >}}
`performance.now()` API got its accuracy reduced from a range of nanoseconds to a
microsecond precision in all major browsers, to mitigate some [XS-Leaks](httos://TODO) [^1] [^2] [^3].

[^1]: Reduce resolution of performance.now (Webkit). [link](https://bugs.webkit.org/show_bug.cgi?id=146531)
[^2]: Reduce precision of performance.now() to 20us (Gecko). [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1427870)
[^3]: Reduce resolution of performance.now to prevent timing attacks (Blink). [link](https://bugs.chromium.org/p/chromium/issues/detail?id=506723)
{{< /hint >}}
<!--TODO(manuelvsousa): Change references to actual wiki articles-->

## Implicit Clocks

### SharedArrayBuffer

 dsadassaddsa asfxd[^1]

```javascript
var buffer = new SharedArrayBuffer(16);
var counter = new Worker("counter.js");
counter.postMessage([buffer],[buffer]);
var arr = new UintArray(buffer);
relative_time = arr[0];
```

`counter.js`

```javascript
self.onmessage = function(event){
  var[buffer] = event.data ;
  var arr = newUintArray(buffer);
  while(1){
    arr[0]++;
  }
}
```

{{< hint info >}}
`SharedArrayBuffer` was removed from browsers with the appearance of [Spectre](https://spectreattack.com/). Later in 2020, it was reintroduced, but now requiring documents to be in a [secure context](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer) to make use of the API. This requirement prevents `SharedArrayBuffer` from being used as an implicit clock.
{{< /hint >}}

### Other implicit clocks

There are a considerable amount of APIs attackers can abuse to create implicit clocks: [`Broadcast Channel API`](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API), [Message Channel API](https://developer.mozilla.org/en-US/docs/Web/API/MessageChannel), CSS animations and others [^2].

[^1]: Shared memory: Side-channel information leaks, [link](https://github.com/tc39/ecmascript_sharedmem/blob/master/issues/TimingAttack.md)
[^2]: Fantastic Timers and Where to Find Them: High-Resolution Microarchitectural Attacks in JavaScript, [link](https://gruss.cc/files/fantastictimers.pdf)