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

Network Timing side-channels have been present on the web since its beginning [^1]. These attacks gained different levels of impact over time, gaining new attention with the beginning of the modern web when browsers started shipping high precision timers like [performance.now()]({{< ref "clocks.md#performancenow" >}}).

To obtain timing measurements attackers must use a [clock]({{< ref "clocks.md" >}}), either an implicit or explicit one. These clocks are usually interchangeable and only vary in accuracy. For simplicity, this article will only address the `performance.now()` API, an explicit clock present in all modern browsers.

This side-channel allows attackers to infer information from a cross-site request based on how much time it takes to complete a request [^2]. The timing measurement varies based on a user state and its usually connected to:

- Resource Size
- The computation time of the backend
- Amount of sub-resources

{{< hint info >}}
Learn more about the different types of clocks in the [Clocks Article]({{< ref "clocks.md" >}}).
{{< /hint >}}

### Modern Web Timing Attacks

The [performance.now()]({{< ref "clocks.md#performancenow" >}}) API can be used to measure how much time it takes to perform a request.

```javascript
let before = performance.now()
await fetch("//mail.com/search?q=foo")
let request_time = performance.now() - before
```

### Frame Timing Attacks

This mechanism allows an attacker to measure a page full load which includes subresources. If [Framing Protections](https://TODO) are in place, only the network request can be measured since its subresources are not fetched.

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

### Cross-window Timing Attacks

If a page sets [Framing Protections](https://TODO), an attacker can still measure a page full load by navigating the victim with `window.open`.

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


### Timeless Timing Attacks

dsadsadsadsadsa [^3]


<!--TODO(manuelvsousa): Add case scenarios -->

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/sec-fetch.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| Modern Timing Attacks              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| Frame Timing |         ✔️       |      ✔️         |  ❌   |          ❌         |
| Cross-window Timing  |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         |
| Timeless Timing  |         ✔️ (if Strict)       |      ✔️         |  ❌   |          ❌         |

[^1]: Exposing Private Information by Timing Web Applications. [link](https://crypto.stanford.edu/~dabo/papers/webtiming.pdf)
[^2]: The Clock is Still Ticking: Timing Attacks in the Modern Web. [link](https://tom.vg/papers/timing-attacks_ccs2015.pdf)
[^3]: Timeless Timing Attacks: Exploiting Concurrency to Leak Secrets over Remote Connections. [link](https://www.usenix.org/system/files/sec20-van_goethem.pdf)
