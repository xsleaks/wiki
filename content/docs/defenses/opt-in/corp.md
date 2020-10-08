+++
title = "Cross-Origin-Resource-Policy"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

Cross-Origin-Resource-Policy (CORP) response header enforces [Cross-Origin Read Blocking]({{< ref "../browser-default/corb.md" >}}) (CORB) as it provides more protection by covering more resources [^1]. Both protect against speculative side-channel attacks (Spectre) by preventing attackers from loading certain cross-origin resources in elements like `script`, `img`, `video`, etc. Unlike CORB, this protection is enforced in the browser only if an application **opts into the protection**. Applications can define which groups of origins (same-site, same-origin, cross-site) are allowed to read their resources.

If an application sets a certain resource CORP Header as same-site or same-origin, an attacker origin is incapable of reading that resource because is not part of the CORP group of origins.

{{< hint good >}}
[Cross-Origin Read Blocking (CORB)]({{< ref "../browser-default/corb.md" >}}), a similar protection, is default browser mechanism based on heuristics to prevent attackers from loading certain cross-origin resources in unlikely scenarios.
{{< /hint >}}

{{< hint warning >}}
This mechanism introduced [a new XS-Leak]({{< ref "../../attacks/browser-features/corp.md" >}}), which allows attackers detect wether CORP was enforced in a certain request.
{{< /hint >}}

## References

[^1]: Cross-Origin Resource Policy (CORP), [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cross-Origin_Resource_Policy_(CORP))