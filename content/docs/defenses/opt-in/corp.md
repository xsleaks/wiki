+++
title = "Cross-Origin-Resource-Policy"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

Cross-Origin Resource Policy (CORP) is a web platform security feature that allows websites to prevent certain resources from being loaded by other origins. This protection complements CORB since it is an opt-in defense whereas CORB blocks some cross-origin reads by default. It is designed to protect against both speculative execution attacks and XS-Leaks by allowing developers to ensure that sensitive resources cannot end up in attacker controlled processes. Unlike CORB, this protection is enforced in the browser only if an application opts in to the protection. Applications can define which groups of origins (same-site, same-origin, cross-site) are allowed to read their resources.

If an application sets a certain resource CORP Header as same-site or same-origin, an attacker origin is incapable of reading that resource. This is a very strong and highly encouraged protection. 

{{< hint warning >}}
CORP does not protect against navigational requests. This means that in browsers that do not support out-of-process iframes, a CORP protected resource may still end up in another origin's process if [framing protections]({{< ref "../opt-in/xfo.md" >}}) are not used. 
{{< /hint >}}

{{< hint warning >}}
This mechanism introduced [a new XS-Leak]({{< ref "../../attacks/browser-features/corp.md" >}}), which allows attackers to detect wether CORP was enforced in a certain request.
{{< /hint >}}

## References

[^1]: Cross-Origin Resource Policy (CORP), [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cross-Origin_Resource_Policy_(CORP))