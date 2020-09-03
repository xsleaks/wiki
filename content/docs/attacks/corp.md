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

{{< hint warning >}}
To avoid this issue, applications should guarantee `CORP` is deployed in all endpoints and application behaviors.
{{< /hint >}}