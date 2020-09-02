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

A way measure the network timing of a request consists in abusing the socket pool of a browser [^1]. Browsers use sockets to communicate, and since the Operating System and the hardware where it runs impose a limit, browsers have to impose one as well. 

To attack this limit, attackers can exhaust the pool in the following way:
1. The Browser has a limit of {{< katex>}}256{{< /katex >}} global sockets and 6 per origin.
2. The Attacker blocks {{< katex>}}255{{< /katex >}} sockets for a long period of time (sleep) by performing {{< katex>}}255{{< /katex >}} requests to different hosts.
3. The Attacker will use the {{< katex>}}256^{th}{{< /katex >}} socket by performing a request to the target page.
4. The attacker makes the {{< katex>}}257^{th}{{< /katex >}} request to another host. Since all the sockets are being used (in step 2 and 3), this request **must** wait until the pool gets an available socket. This waiting period will give the attacker the network timing of the {{< katex>}}256^{th}{{< /katex >}} socket, which belongs to the target page. This occurs because the {{< katex>}}255{{< /katex >}} sockets in step 2. are **still blocked**, so if the pool got an available socket it was caused by the release of the socket in step 3 which is directly connected with the time taken to complete the request.


{{< hint info >}}
Similarly to [partitioned caches](https://TODO), some browsers are considering extending the principle of split per site/origin the quotas for [socket pools](https://github.com/whatwg/fetch/issues/904#issuecomment-509275330).
{{< /hint >}}

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| Socket Exhaust      |         ✔️ (if Strict)         |      ✔️         |  ❌   |          ❌         |


[^1]: Leak cross-window request timing by exhausting connection pool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=843157)