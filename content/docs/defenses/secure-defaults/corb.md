+++
title = "Cross-Origin Read Blocking"
description = ""
date = "2020-10-01"
category = "Defense"
menu = "main"
+++

Cross-Origin Read Blocking (CORB) is a security mechanism that prevents attackers from loading certain cross-origin resources [^1]. This protection was created to defend against speculative side-channel attacks such as Spectre that allow attackers to read the memory of the process that both cross-site pages (e.g. *attacker.com* and *sensitive.com*) were embedded into. CORB aims to prevent attackers from loading certain sensitive cross-origin resources into an attacker-controlled process. For example, if an attacker tries to load cross-origin HTML, XML, or JSON into an `img` tag, CORB prevents this from happening. With CORB, the scenario is treated as though the server returned no data.

To classify resources, CORB uses the `Content-Type` header, the `nosniff` header, and a variety of other heuristics.

{{< hint info >}}
[Cross-Origin Resource Policy (CORP)]({{< ref "../opt-in/corp.md" >}}) is an opt-in protection which enforces and extends CORB.
{{< /hint >}}

When using CORB, be aware of the following facts:

* Currently, only Chromium-based browsers support CORB.
* CORB does not protect against navigational requests. This means that in browsers that do not support out-of-process iframes, a CORB-protected resource may still end up in another origin's process if [framing protections]({{< ref "../opt-in/xfo.md" >}}) are not used.
* CORB introduces a [new XS-Leak]({{< ref "../../attacks/browser-features/corb.md" >}}) technique since attackers may be able to observe the results of CORB. This can lead to a variety of information leaks. However, in most cases, these information leaks have a lower impact than the data that could be leaked via speculative execution attacks.


## References

[^1]: Cross-Origin Read Blocking for Web Developers, [link](https://chromium.googlesource.com/chromium/src/+/master/services/network/cross_origin_read_blocking_explainer.md)
