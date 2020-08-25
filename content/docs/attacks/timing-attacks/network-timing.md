+++
title = "Network Timing"
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

Network Timing side-channels have been present on the web since its beginning [^1]. These attacks gained different levels of impact over time, gaining new attention with the beginning of the modern web when browsers started shipping high precision timers like `performance.now()`.

To obtain timing measurements attackers must use a [clock]({{< ref "clocks.md" >}}), either an implicit or explicit one. For simplicity, this section will address only the `performance.now()` API, an explicit clock present in all modern browsers.

This side-channel allows attackers to infer information from a cross-site request based on how much time it took to request it.

{{< hint info >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "clocks.md" >}}).
{{< /hint >}}

## Modern Web Timing Attacks

[`performance.now()`]({{< ref "clocks.md#performancenow" >}}) API [^2]

```javascript
let before = performance.now()
await fetch("//mail.com/search?q=foo")
let request_time = performance.now() - before
```

{{< hint info >}}
`performance.now() had its acuracy reduced for allowing [Clocks Article](https://TODO).
{{< /hint >}}

## Frame Timing


```html
<iframe name=f id=g></iframe>
<script>
before = performance.now();
f.location = '//mail.com/search?q=foo';
g.onerror = g.onload = ()=>{
    console.log('time was', performance.now() - before)
};
</script>
```

### Cross-window timing 

```javascript
let w=0, z=0, v=performance.now();
onmessage=()=>{
  try{
    if(w && w.document.cookie){
      // still same origin
    }
    postMessage('','*');
  }catch(e){
    z=performance.now();
    console.log('time to load was', z - v);
  }
};
postMessage('','*');
w=open('//www.google.com/robots.txt');
```


## Network Timing

asddsadsadas [^3]


## Network Timing 2

dsadsadsadsadsa [^4]


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/sec-fetch.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| Modern Timing Attacks              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| Frame Timing |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         |
| Cross-window timing  |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         |
| Network Timing  |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         |
| Network Timing 2  |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         |

[^1]: Exposing Private Information by Timing Web Applications. [link](https://crypto.stanford.edu/~dabo/papers/webtiming.pdf)
[^2]: The Clock is Still Ticking: Timing Attacks in the Modern Web. [link](https://tom.vg/papers/timing-attacks_ccs2015.pdf)
[^3]: HEIST: HTTP Encrypted Information can be Stolen through TCP-windows. [link](https://www.blackhat.com/docs/us-16/materials/us-16-VanGoethem-HEIST-HTTP-Encrypted-Information-Can-Be-Stolen-Through-TCP-Windows-wp.pdf)
[^4]: Timeless Timing Attacks: Exploiting Concurrency to Leak Secrets over Remote Connections. [link](https://www.usenix.org/system/files/sec20-van_goethem.pdf)
