+++
title = "CORP Leaks"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++

## Explanation

[Cross-Origin Resource Policy](https://TODO) (CORP) is a web platform security feature which is an An important complement of [CORB](https://TODO). Unfortunately, similarly to [CORB XS-Leaks](https://TODO) applications can introduce a new XS-Leak if they misconfigure the use of this protection.

A webpage will introduce an XS-Leak if `CORP` is enforced based on user data. If a page search feature enforce `CORP` when showing results, but doesn't when returning no results (`204 No Content`), an attacker will be able to distinguish the two cases. An attacker trying to fetch a page protected by `CORP` will receive a network error, and nothing otherwise.

## Defense

An application can avoid this XS-Leak if it guarantees `CORP` is deployed in all application resources. However, the generic security mechanism that are able to invalidate cross-site requests based on user sessions can also prevent this attack.

| [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata](https://TODO)  | [Cross-Origin-Opener-Policy]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:------------------:|:---------------:|:-----:|:--------------------:|
|         ✔️         |      ✔️         |  ❌   |          ❌         |