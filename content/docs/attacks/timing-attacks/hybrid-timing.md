+++
title = "Hybrid Timing"
description = ""
date = "2020-10-01"
category = "Attack"
abuse = [
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP",
]
menu = "main"
weight = 3
+++

Hybrid Timing Attacks allow attackers to measure the sum of a group of factors that influence the final timing measurement. These factors include:

- [Network delays]({{< ref "network-timing.md" >}})
- Document parsing
- Retrieval and processing of subresources
- [Code execution]({{< ref "execution-timing.md" >}})

Some of the factors differ in value depending on the application. This means that [Network Timing]({{< ref "network-timing.md" >}}) might be more significant for pages with more backend processing, while [Execution Timing]({{< ref "execution-timing.md" >}}) can be more significant in applications processing and displaying data within the browser. Attackers can also eliminate some of these factors to obtain more precise measurements. For example, an attacker could preload all of the subresources by embedding the page as an `iframe` (forcing the browser to cache the subresources) and then perform a second measurement, which excludes any delay introduced by the retrieval of those subresources.

##  Frame Timing Attacks (Hybrid)

If a page does not set [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}), an attacker can obtain a hybrid measurement that considers all of the factors. This attack is similar to a [Network-based Attack]({{< ref "network-timing.md#frame-timing-attacks-network" >}}), but when the resource is retrieved, the page is rendered and executed by the browser (subresources fetched and JavaScript executed). In this scenario, the `onload` event only triggers once the page fully loads (including subresources and script execution).

```javascript
var iframe = document.createElement('iframe');
// Set the URL of the destination website
iframe.src = "https://example.org";
document.body.appendChild(iframe);

// Measure the time before the request was initiated
var start = performance.now();

iframe.onload = () => {
  // When iframe loads, calculate the time difference
  var time = performance.now() - start;
  console.log("The iframe and subresources took %d ms to load.", time)
}
```

## Defense

|  Attack Alternative   | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})   |
| :-------------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :----------------------------------------------------------------------: |
| Frame Timing (Hybrid) |                                         ✔️                                          |                          ❌                          |                                 ✔️                                 | [FIP]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}}) |
