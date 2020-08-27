+++
title = "Fetch Metadata"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Fetch Metadata Headers are sent by browsers in every request or navigation made by a page. These Headers provide context on how every request/navigation was initiated, so that applications are able to make more informed decisions on how to respond to them. This allows servers to behave differently when they detect potential attacks (i.e. unexpected cross-origin requests)[^1].

## Considerations

Fetch Metadata Headers are an **in-depth Defense Mechanism** and should **not be seen as a replacement** for mechanisms such as [Same-Site Cookies]({{< ref "same-site-cookies.md" >}}), [Cross-Origin-Opener-Policy]({{< ref "coop.md" >}}) or [Framing Protections]({{< ref "xfo.md" >}}). Even though Fetch Metadata Headers share some capabilities with these mechanisms (i.e act differently upon cross-site requests), **they do not provide a safe default**. 
Its success is dependent on the application coverage and deployment correctness.

Nevertheless, minding the limitations, this mechanism can be very effective against cross-origin attacks if applied correctly. It also protects applications against attacks like `XSSI`, `Clickjacking` and `CSRF`. 

## Deployment

Check out this [web.dev](https://web.dev/fetch-metadata/) article to learn more about the advantages of this protection and how to deploy it.

## XS-Leaks Mitigation Overview

|                           XS-Leak                                 |  Full Mitigation   |
|:-----------------------------------------------------------------:|:-------------------:
| [Frame Counting]({{< ref "../../attacks/frame-counting.md" >}})   |         ✔️
| [Navigations]({{< ref "../../attacks/navigations.md" >}})         |         ✔️
| [ID Leaks]({{< ref "../../attacks/id-attribute.md" >}})               |         ✔️

### Table Caption

- **Full Mitigation** - Fully mitigates some XS-Leaks if correctly implemented in all application endpoints. *See limitations in [Considerations]({{< ref "fetch-metadata.md#considerations" >}})*

[^1]: Protect your resources from web attacks with Fetch Metadata, [link](https://web.dev/fetch-metadata/)
