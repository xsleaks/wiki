+++
title = "Fetch Metadata"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Fetch Metadata Headers are sent by browsers in every request or navigation made by a page. These headers intend to provide context on how the request/navigation was initiated, so that applications, upon receiving them, are able to make smarter decisions on how to respond to them. This allow applications to behave differently when they sense insecure situations (i.e cross-origin requests)[^1].

## Considerations

Fetch Metadata Headers are an **in-depth Defense Mechanism** and should **not be seen as a replacement** for mechanisms such as [Same-Site Cookies]({{< ref "same-site-cookies.md" >}}), [Cross-Origin-Opener-Policy]({{< ref "coop.md" >}}) or [Framing Protections]({{< ref "xfo.md" >}}). Even though it shares some capabilities with these mechanisms (i.e act differently upon cross-site requests), **it does not provide a safe default**. 
Its success is dependent on the application coverage and deployment correctness.

This mechanism can be very effective again cross-origin attacks if applied correctly. Nevertheless, keep in mind its limitations. This mechanism can also protect applications from attacks like `XSSI` and `Clickjacking` and `CSRF`. 

## Deploy

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about the the advantages of this protection and how to deploy it.

## XS-Leaks Mitigation Overview

|                           XS-Leak                                 |    Fetch Metadata Mitigation   |  Full Mitigation   |
|:-----------------------------------------------------------------:|:------------------------------:|:-------------------:
| [Frame Counting]({{< ref "../../attacks/frame-counting.md" >}})   |         ✔️                     |         ✔️
| [Navigations]({{< ref "../../attacks/navigations.md" >}})         |         ✔️                     |         ✔️
| [ID Leaks]({{< ref "../../attacks/id-leaks.md" >}})               |         ✔️                     |         ✔️

### Table Caption

- **Fetch Metadata Mitigation** -  At least one attack alternative of the XS-Leak relies on `iframes`
- **Full Mitigation** - Fully mitigates an XS-Leak if fully implemented in all application endpoints. *See warning in [Considerations]({{< ref "fetch-metadata.md#considerations" >}})*


[^1]: Protect your resources from web attacks with Fetch Metadata, [link](https://web.dev/fetch-metadata/)