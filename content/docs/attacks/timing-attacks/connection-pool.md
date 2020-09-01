+++
title = "Connection Pool"
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

SomeBrowsers have limits

Connection Pool


[^1]



1. The attackers blocks a




{{< hint info >}}
Similarly to [partitioned caches](https://TODO), some browsers are considering extending the principle of split per site/origin the quotas for [socket pools](https://github.com/whatwg/fetch/issues/904#issuecomment-509275330).
{{< /hint >}}

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| Socket Exhaust      |         ✔️ (if Strict)         |      ✔️         |  ❌   |          ❌         |


[^1]: Leak cross-window request timing by exhausting connection pool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=843157)