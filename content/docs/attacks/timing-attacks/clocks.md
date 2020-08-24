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

There are two types of clocks: Implicit and explicit clocks. Explicit clocks are the ones developers use to get direct timing measurements and such mechanisms are offered **explicitly** by the browser. Implicit clocks, are created to get timing measurements


## Explicit Clocks

### performance.now

The [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now) API. Unlike previous options (`Date.now`), this API offers 



{{< hint warning >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "clocks.md" >}}).
{{< /hint >}}

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




https://bugs.webkit.org/show_bug.cgi?id=146531
https://bugs.chromium.org/p/chromium/issues/detail?id=506723