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

There are two types of clocks: Implicit and explicit clocks. Explicit clocks are the ones developers use to get direct timing measurements and such mechanisms are offered **explicitly** by the browse with that purpose in mind. On the other hand, Implicit clocks misuse particular web features to create unintended clocks that create a relative timing.


## Explicit Clocks

### performance.now

The [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) API. Unlike previous options (`Date.now`), this API offers 



{{< hint info >}}
`performance.now()` API got its accuracy reduced from a range of nanoseconds to a
microsecond precision in all major browsers, to mitigate some [XS-Leaks](httos://TODO) [^1] [^2] [^3].

[^1]: Reduce resolution of performance.now. [link](https://bugs.webkit.org/show_bug.cgi?id=146531)
[^2]: Reduce precision of performance.now() to 20us. [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1427870)
[^3]: Reduce resolution of performance.now to prevent timing attacks. [link](https://bugs.chromium.org/p/chromium/issues/detail?id=506723)
{{< /hint >}}
<!--TODO(manuelvsousa): Change references to actual wiki articles-->

## Implicit Clocks

### SharedArrayBuffer

```javascript
var buffer = new SharedArrayBuffer(16);
var counter = new Worker("counter.js");
counter.postMessage([buffer],[buffer]);
var arr = new UintArray(buffer);
[...]
timestamp = arr[0];
```

#### Case Scenarios

https://gruss.cc/files/fantastictimers.pdf
https://github.com/tc39/ecmascript_sharedmem/blob/master/issues/TimingAttack.md
