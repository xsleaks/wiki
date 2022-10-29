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

Another way to measure the network timing of a request consists of abusing the socket pool of a browser [^1]. Browsers use sockets to communicate with servers. As the operating system and the hardware it runs on have limited resources, browsers have to impose a limit.

To exploit the existence of this limit, attackers can:
1. Check what the limit of the browser is, for example 256 global sockets. [^2]
2. Block {{< katex>}}255{{< /katex >}} sockets for a long period of time by performing {{< katex>}}255{{< /katex >}} requests to different hosts that simply hang the connection
```javascript
for(let i=0; i<255; i++) fetch('https://'+i+'.example.com/', {mode: "no-cors", cache: "no-store"});
```
4. Use the {{< katex>}}256^{th}{{< /katex >}} socket by performing a request to the target page.
5. Perform a {{< katex>}}257^{th}{{< /katex >}} request to another host. Since all the sockets are being used (in steps 2 and 3), this request must wait until the pool receives an available socket. This waiting period provides the attacker with the network timing of the {{< katex>}}256^{th}{{< /katex >}} socket, which belongs to the target page. This works because the {{< katex>}}255{{< /katex >}} sockets in step 2 are still blocked, so if the pool received an available socket, it was caused by the release of the socket in step 3. The time to release the {{< katex>}}256^{th}{{< /katex >}} socket is directly connected with the time taken to complete the request.
```javascript
performance.clearResourceTimings();
await fetch(location.href, {cache: "no-store"});
await new Promise(r => setTimeout(r, 1000));
let data = performance.getEntries().pop();
let type = (data.connectStart === data.startTime) ? 'reused' : 'new';
console.log('Time spent: ' + data.duration + ' on ' + type + ' connection.');
```

## Connection reuse
With HTTP/1.1 and HTTP/2 and HTTP/3 requests may reuse an existing connection for a host to improve performance. [^3][^4]
Since a resused connection is normaly faster this could allow for detecting if a site has connected to a host excluding anything thats been cached and leaking infomation about the cross-site request by abusing Stream prioritization and HPACK compression. [^5]
Connections may get closed if there left idle, for example 60 secounds or 30 for HTTP/3. [^2][^6]
This may leak when a connection happend.
```javascript
// Detect if HTTP/3 request to host was not made in the last 20 secounds.
await new Promise(r => setTimeout(r, 10000));
// Check for connection reuse
```

## Defense

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) | [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}}) |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ❌                                 |                                   ❌                                   |


{{< hint info >}}
Similar to [partitioned caches]({{< ref "../../defenses/secure-defaults/partitioned-cache.md" >}}), some browsers are considering to extend the principle of "split per site/origin" of resources to [socket pools](https://bugzilla.mozilla.org/show_bug.cgi?id=1572544).
{{< /hint >}}

## References

[^1]: Leak cross-window request timing by exhausting connection pool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=843157)
[^2]: client_socket_pool_manager.cc, [link](https://source.chromium.org/chromium/chromium/src/+/main:net/socket/client_socket_pool_manager.cc)
[^3]: rfc9113 Connection Reuse, [link](https://httpwg.org/specs/rfc9113.html#rfc.section.9.1.1)
[^4]: rfc9114 Connection Reuse, [link](https://httpwg.org/specs/rfc9114.html#rfc.section.3.3)
[^5]: rfc9113 Remote Timing Attacks, [link](https://httpwg.org/specs/rfc9113.html#rfc.section.10.9)
[^6]: quic_context.h, [link](https://source.chromium.org/chromium/chromium/src/+/main:net/quic/quic_context.h)
