+++
title = "CORP Leaks"
description = ""
date = "2020-10-01"
category = "Attack"
abuse = [
    "Browser Feature",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
]
menu = "main"
weight = 2
+++

## Explanation

[Cross-Origin Resource Policy]({{< ref "/docs/defenses/opt-in/corp.md" >}}) (CORP) is a web platform security feature that allows websites to prevent certain resources from being loaded by other origins. This protection complements [CORB]({{< ref "/docs/defenses/secure-defaults/corb.md" >}}) since it is an opt-in defense, whereas CORB blocks some cross-origin reads by default. Unfortunately, similar to [CORB]({{< ref "corb.md" >}}), applications can introduce a new XS-Leak if they misconfigure the use of this protection.

A webpage will introduce an XS-Leak if `CORP` is enforced based on user data. If a page search feature enforces `CORP` when showing results, but doesn't do so when returning no results, an attacker will be able to distinguish the two scenarios. This occurs because a page/resource protected by `CORP` will return an error when fetched cross-origin.  [^demo]

## Defense

An application can avoid this XS-Leak if it guarantees `CORP` is deployed in all application resources/endpoints. Moreover, generic security mechanisms that allow the invalidation of cross-site requests will also help prevent this attack.

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                          |                          ❌                          |                                 ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |

🔗 – Defense mechanisms must be combined to be effective against different scenarios.

## References

[^demo]: Detect Cross-Origin-Opener-Policy header with popup., [link](https://xsinator.com/testing.html#COOP%20Leak)
