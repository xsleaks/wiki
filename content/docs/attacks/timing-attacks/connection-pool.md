+++
title = "Connection Pool"
description = ""
date = "2020-10-01"
category = "Attack"
abuse = [
    "Connection Pool",
    "Browser Limits",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
]
menu = "main"
+++

Another way to measure the network timing of a request consists of abusing the socket pool of a browser [^1]. Browsers use sockets to communicate with servers and since the Operating System and the hardware it runs on have limited resources, browsers have to impose a limit.

To exploit the existence of this limit attackers can:
1. Check what is the limit of the browser, for example 256 global sockets.
2. Block {{< katex>}}255{{< /katex >}} sockets for a long period of time by performing {{< katex>}}255{{< /katex >}} requests to different hosts that simply hang the connection
3. Use the {{< katex>}}256^{th}{{< /katex >}} socket by performing a request to the target page.
4. Perform a {{< katex>}}257^{th}{{< /katex >}} request to another host. Since all the sockets are being used (in steps 2 and 3), this request must wait until the pool gets an available socket. This waiting period will give the attacker the network timing of the {{< katex>}}256^{th}{{< /katex >}} socket, which belongs to the target page. This occurs because the {{< katex>}}255{{< /katex >}} sockets in step 2. are still blocked, so if the pool got an available socket it was caused by the release of the socket in step 3. The time to release the {{< katex>}}256^{th}{{< /katex >}} socket is directly connected with the time taken to complete the request.

## Defense

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) | [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}}) |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ❌                                 |                                   ❌                                   |


{{< hint info >}}
Similarly to [partitioned caches]({{< ref "../../defenses/secure-defaults/partitioned-cache.md" >}}) some browsers are considering to extend the principle of "split per site/origin" of resources to [socket pools](https://bugzilla.mozilla.org/show_bug.cgi?id=1572544).
{{< /hint >}}

## References

[^1]: Leak cross-window request timing by exhausting connection pool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=843157)
