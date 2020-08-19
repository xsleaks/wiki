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

## Explanation

dsaadssadasd
### Explicit V.s Implicit Clocks


## Explicit Clocks

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