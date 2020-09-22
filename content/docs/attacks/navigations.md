+++
title = "Navigations"
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

Detecting if a cross-site page triggered a navigation can be useful to an attacker.  This can be done in two ways:
- Using an `iframe` and counting the number of times the `onload` event is triggered.
- Checking the value of `History.length`, accessible through any `window` reference, gives the number of entries in the history of a victim either changed by `History.pushState` or regular navigations. To get the value of `History.length` an attacker changes the location of the `window` reference with the target website, changes back to same-origin, and finally reads the value.

## Download Trigger

When endpoints set the [`Content-Disposition: attachment`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition) Header, it forces the browser to download the response as an attachment instead of navigating to it. Detecting if this behavior occurred might allow attackers to leak private information, considering it as a state

### Download bar

In Chromium-based browsers when a file is downloaded, a preview of the download process appears in a bar at the bottom, integrated into the browser window. By monitoring the window height attackers could detect whether the "download bar" opened.


```javascript

// Any Window reference (can also be done using an iframe in some cases)
const tab = window.opener;

// The current window height
const screenHeight = window.innerHeight;

// The size of the chrome download bar on, for example, mac os x
const downloadsBarSize = 49;

tab.location = 'https://target';

setTimeout(() => {
    let margin = screenHeight - window.innerHeight;
    if (margin === downloadsBarSize) {
       return console.log('downloads bar detected');
    }
}, 5 * 1000);
```

{{< hint info >}}
This attack is only possible in Chromium-based browsers.
{{< /hint >}}

### Download Navigation

Another way to test for the [`Content-Disposition: attachment`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition) Header is to check if a navigation occurred. If a page load triggers a download, it will not trigger a navigation. 

1. Open an attacker origin with `window.open` and save the window reference.
2. Navigate the saved reference to the endpoint that might download.
3. After a timeout, check if the window is still same-origin

The snippet presented in the [Cross-Window Timing](httpps://TODO) XS-Leak can be slightly adapted to detect this behavior.

#### Download Navigation (without timeout)

The following snippet can do a more precise measurement, without relying on timeouts and imprecise timings. The attack works as follows:

1. Include an iframe (inner) inside an iframe (outer). The inner iframe embeds the target website.
2. If the target website triggers a download the inner iframe origin will **remain** `about:blank` (downloads don’t navigate).
3. The outer iframe waits until `onload` triggers. The observation is that even though the download attempt doesn't trigger an `onload` event the window still "waits" for the resource to be downloaded.
4. If a navigation occurs, the inner iframe will change origin's and both will now be different. 
5. Finally, the outer iframe verifies if `i.contentWindow.location.href` (line 8) is accessible, only possible if both iframes share the same origin (Same-Origin Policy is enforced). If both iframes are in different origins, a `DOMException` will be thrown, meaning a navigation occurred.

{{< highlight javascript "linenos=table,linenostart=1" >}}
onmessage = e => console.log(e.data);
var outer = document.createElement('iframe');
var url = 'https://target';
outer.src = `data:text/html,\
            <iframe id='inner' src="${url}" ></iframe>
            <!-- onload is part of outer iframe -->
            <script>onload=()=>{
                try {
                    i.contentWindow.location.href;
                    top.postMessage('download attempt','*');
                } catch(e) {
                    top.postMessage('no download','*');
                }
            }%3c/script>`;
outer.onload = ()=>{outer.remove();}
document.body.appendChild(outer);
{{< / highlight >}}

<!-- 
## Case Scenarios

- An online bank decides to redirect wealthy users to unmissable stock opportunities by triggering a navigation to a reserved space in the website when users are consulting the account balance. If this is only done to a specific group of users, it becomes possible for an attacker to leak the "client status" of the user. -->
<!--TODO(manuelvsousa): Add better examples-->
<!--TODO(manuelvsousa): evaluate if we are keeping case scenarios in the wiki-->


## Server-Side Redirects

### Inflation

A server-side redirect can be detected from a cross-origin page when the destination URL increase in size and reflects a user input, either in the form of a query string parameter or a path. The following technique relies on the fact it is possible to induce an error in most web-servers by overloading the request parameters/path. Since the redirect increases the size of the URL, it can be detected by sending exactly one character less than the server maximum capacity, that way, if the size increases the server will respond with an error code which can be detected from a cross-origin page using common DOM APIs.

<!-- ### CSP Violations -->
<!--TODO(manuelvsousa): I will discuss CSP violations with @lweichselbaum to know if it's still thing-->


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| iframe                             |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| Download bar                       |         ✔️                 |      ✔️         |  ✔️   |          ✔️         |
| Download Navigation (w/ timeout)   |         ✔️ (If Strict)     |      ✔️         |  ❌   |          ❌         |
| Download Navigation (no timeout)   |         ✔️                 |      ✔️         |  ✔️   |          ✔️         |
| `History.length` (iframe)          |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (window.open)     |         ✔️ (If Strict)     |      ✔️         |  ✔️   |          ❌         |

## Real World Example

A vulnerability reported to Twitter used this technique to leak the contents of private tweets using [XS-Search](https://TODO). This attack was possible because the page would only trigger a navigation depending on whether there were results to the user query [^1].

## References

[^1]: Protected tweets exposure through the url, [link](https://hackerone.com/reports/491473)
