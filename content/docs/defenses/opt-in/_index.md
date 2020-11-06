---
title: "Opt-In Mechanisms"
weight: 1
---

# Opt-In Mechanisms

There are many different opt-in mechanisms that applications can deploy to defend against XS-Leaks. Note that mechanisms can overlap in terms of the techniques they defend against. 

* [Fetch Metadata]({{< ref "./fetch-metadata.md" >}}) allows the server to determine how and why a request was initiated so that it can choose to reject any malicious requests. 
* [Cross-Origin-Opener-Policy]({{< ref "./coop.md" >}}) allows an application to prevent other websites from interacting with it via `window.open` or `window.opener`.
* [Cross-Origin-Resource-Policy]({{< ref "./corp.md" >}}) allows an application to prevent other sites from including specific resources.
* [Framing Protections]({{< ref "./xfo.md" >}}) allow an application to define what sites are allowed to frame it.
* [Same-Site Cookies]({{< ref "./same-site-cookies.md" >}}) allow an application to change which requests from third party sites include cookies.
