+++
title = "Cross-Origin-Resource-Policy"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


## Explanation

Cross-Origin-Resource-Policy (CORP) response header is a complement of [Cross-Origin Read Blocking]({{< ref "../browser-intrinsic/corb.md" >}}) (CORB). CORP allows applications to **opt in to protection** against other resource requests that might not be covered automatically by CORB itself[^1]. Applications can define which origins are allowed to read its resources.

If an application sets a certain resource CORP Header as `same-site`, an attacker, which is in a different origin is incapable of reading that resource.

## Considerations


[^1]: Cross-Origin Resource Policy (CORP), [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cross-Origin_Resource_Policy_(CORP))
<!-- [^2]: Cross-Origin Read Blocking for Web Developers, [link](https://www.chromium.org/Home/chromium-security/site-isolation) -->
