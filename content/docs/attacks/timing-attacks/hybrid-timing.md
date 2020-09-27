+++
title = "Hybrid Timing"
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

Hybrid Timing Attacks allow attackers to measure a combination of factors that influence the final timing measurement. These factors can be:

- [Network Timing](https://TODO).
- Time to parse the document.
- Retrieve and load subresources.
- [Execution Timing](https://TODO).

Some of the factors differ in value depending on the application. This means that [Network Timing](https://TODO) might be more observable in pages with more backend processing while [Execution Timing](https://TODO) can be more observable in applications processing and displaying data within the browser. Attackers can also eliminate some of the factors of the equation to obtain better measurements, for example, one could preload all the subresources by embedding the page as an iframe (forcing the browser to cache them) and do a second measurement which will exclude any delay introduced by the retrieval of those subresources.

###  Frame Timing Attacks (Hybrid)

If a page does not set [Framing Protections](https://TODO), an attacker can obtain a hybrid measurement that considers all the factors. This attack is similar to the [Network-based Attack](https://TODO-frame-timing-attacks-network), but when the resource arrives at the browser the page is rendered and executed by the browser (subresources retrieved and JavaScript executed). In this scenario, the `onload` event only triggers when the page fully loads.

```html
<iframe name=f id=g></iframe>
<script>
before = performance.now();
f.location = 'https://target.page';
g.onerror = g.onload = ()=>{
    console.log('time was', performance.now() - before)
};
</script>
```

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:----------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| Frame Timing (Hybrid)  |         ✔️       |      ✔️       |  ❌   |          ✔️          |