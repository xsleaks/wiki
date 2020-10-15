+++
title = "Cross-Origin Read Blocking"
description = ""
date = "2020-10-01"
category = "Defense"
menu = "main"
+++

Cross-Origin Read Blocking (CORB) is a security mechanism that prevents attackers from loading certain cross-origin resources [^1]. This protection was created to defend against speculative side-channel attacks such as Spectre that allow attackers to read the memory of their own process. CORB aims to prevent attackers from loading certain sensitive cross-origin resources into an attacker controlled process. For example, if the attacker tries to load cross-origin HTML, XML, or JSON into an `img` tag, CORB will prevent this from happening. Without CORB, this might also trigger an Error Event due to it not being a valid response. With CORB, it will be treated as though the server returned no data. 

To classify resource types CORB uses the `Content-Type` header if `nosniff` is set. In case the page does not serve a `nosniff` header CORB will, based on heuristics, check if the resource is worth protecting by looking at the beginning of the response body.

{{< hint good >}}
Chrome is currently the only browser with CORB deployed.
{{< /hint >}}

{{< hint good >}}
[Cross-Origin Resource Policy (CORP)]({{< ref "../opt-in/corp.md" >}}) is an opt-in protection which enforces and extends CORB.
{{< /hint >}}


## Considerations

{{< hint warning >}}
CORB introduces a [new XS-Leak]({{< ref "../../attacks/browser-features/corb.md" >}}) technique since attackers may be able to observe the results of CORB. This can lead to a variety of different information leaks. However, in most cases this information leak has a lower impact than the data that could be leaked via speculative execution attacks.
{{< /hint >}}

## References

[^1]: Cross-Origin Read Blocking for Web Developers, [link](https://chromium.googlesource.com/chromium/src/+/master/services/network/cross_origin_read_blocking_explainer.md)
