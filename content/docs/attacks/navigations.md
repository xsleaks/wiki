+++
title = "Navigations"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "Downloads",
    "History",
    "CSP Violations",
    "Redirects",
    "window.open",
    "window.stop",
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP",
    "Framing Protections",
]
menu = "main"
weight = 2
+++

Detecting if a cross-site page triggered a navigation (or didn't) can be useful to an attacker. For example, a website may trigger a navigation in a certain endpoint [depending on the status of the user]({{< ref "#case-scenarios" >}}).

To detect if any kind of navigation occurred, an attacker can:

- Use an `iframe` and count the number of times the `onload` event is triggered.
- Check the value of `history.length`, which is accessible through any window reference. This provides the number of entries in the history of a victim that were either changed by `history.pushState` or by regular navigations. To get the value of `history.length`, an attacker changes the location of the window reference to the target website, then changes back to same-origin, and finally reads the value. [Run demo](https://xsinator.com/testing.html#History%20Length%20Leak)

## Download Trigger

When an endpoint sets the [`Content-Disposition: attachment`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition) header, it instructs the browser to download the response as an attachment instead of navigating to it. Detecting if this behavior occurred might allow attackers to leak private information if the outcome depends on the state of the victim's account.

### Download Navigation (with iframes)

Another way to test for the [`Content-Disposition: attachment`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition) header is to check if a navigation occurred. If a page load causes a download, it does not trigger a navigation and the window stays within the same origin. [Run demo](https://xsinator.com/testing.html#Download%20Detection)

The following snippet can be used to detect whether such a navigation has occurred and therefore detect a download attempt:

```javascript
// Set the destination URL to test for the download attempt
var url = 'https://example.org/';
// Create an outer iframe to measure onload event
var iframe = document.createElement('iframe');
document.body.appendChild(iframe);
// Create an inner iframe to test for the download attempt
iframe.srcdoc = `<iframe src="${url}" ></iframe>`;
iframe.onload = () => {
      try {
          // If a navigation occurs, the iframe will be cross-origin,
          // so accessing "inner.origin" will throw an exception
          iframe.contentWindow.frames[0].origin;
          console.log('Download attempt detected');
      } catch(e) {
          console.log('No download attempt detected');
      }
}
```

{{< hint info >}}
When there is no navigation inside an `iframe` caused by a download attempt, the `iframe` does not trigger an `onload` event directly. For this reason, in the example above, an outer `iframe` was used instead, which listens for an `onload` event which triggers when subresources finish loading, including `iframe`s.
{{< /hint >}}

{{< hint important >}}
This attack works regardless of any [Framing Protections]({{< ref "xfo" >}}), because the `X-Frame-Options` and `Content-Security-Policy` headers are ignored if `Content-Disposition: attachment` is specified.
{{< /hint >}}

### Download Navigation (without iframes)

A variation of the technique presented in the previous section can also be effectively tested using `window` objects:

```javascript
// Set the destination URL
var url = 'https://example.org';
// Get a window reference
var win = window.open(url);

// Wait for the window to load.
setTimeout(() => {
      try {
          // If a navigation occurs, the iframe will be cross-origin,
          // so accessing "win.origin" will throw an exception
          win.origin;
          parent.console.log('Download attempt detected');
      } catch(e) {
          parent.console.log('No download attempt detected');
      }
}, 2000);
```

## Server-Side Redirects

### Max redirects
When a page initiates a chain of 3XX redirects, browsers limit the maximum number of redirects to 20 [^spec-redirects]. This can be used to detect the exact number of redirects occurred for a cross-origin page by following the below approach [^redirect-leak]:
1. As a malicious website, initiate 19 redirects and make the final 20th redirect to the attacked page.
2. If the browser threw a network error, at least one redirect occurred. Repeat the process with 18 redirects.
3. If the browser didn't threw a network error, the number of redirects is known as `20 - issued_redirects`.

*To detect an error one can use [Error Events]({{< ref "/docs/attacks/error-events.md" >}})*

If performed in a top window, this also works with SameSite lax cookies and other cross-site protections, such as [Framing Isolation Policy]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}}) or [Resource Isolation Policy]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}). [Run demo](https://xsinator.com/testing.html#Max%20Redirect%20Leak)

### Inflation (Server-Side Errors)

A server-side redirect can be detected from a cross-origin page if the destination URL increases in size and contains an attacker-controlled input (either in the form of a query string parameter or a path). The following technique relies on the fact that it is possible to induce an error in most web-servers by generating large request parameters/paths. Since the redirect increases the size of the URL, it can be detected by sending exactly one character less than the server's maximum capacity. That way, if the size increases, the server will respond with an error that can be detected from a cross-origin page (e.g. via Error Events).

{{< hint example >}}
An example of this attack can be seen [here](https://xsleaks.github.io/xsleaks/examples/redirect/).
{{< /hint >}}

### Inflation (Client-Side Errors)

Most browsers have a maximum permitted URL length, above which the navigation will be aborted. For example, Chrome limits URLs to a maximum length of 2MB [^chrome-maxlength]. When this limit is exceeded, the browser may exhibit behavior that can be detected from a cross-origin page. The exact URL length limit and oracle behavior depends on the browser.

When requesting a URL with a fragment, the fragment is preserved on server redirects. For example, if `//example.org` redirects to `//example.org/redirected`, the browser will navigate to `//example.org/redirected#fragment` when `//example.org#fragment` is requested. This allows an attacker to artificially inflate the URL length by adding a large fragment to the URL so that it is exactly one character less than the maximum permitted length.

{{< hint example >}}
Chrome will navigate to an `about:blank` page if the URL length is exceeded. An attacker can detect whether a redirect occurred by checking if the page is still on the same origin.
{{< /hint >}}

## Cross-Origin Redirects

### CSP Violations

[Content-Security-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) (CSP) is an in-depth defense mechanism against XSS and data injection attacks. When a CSP is violated, a `SecurityPolicyViolationEvent` is thrown. An attacker can set up a CSP using the [`connect-src` directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/connect-src) which triggers a `Violation` event every time a `fetch` follows an URL not set in the CSP directive. This allows an attacker to detect if a redirect to another origin occurred [^2] [^3]. [Run demo](https://xsinator.com/testing.html#CSP%20Violation%20Leak)

The example below triggers a `SecurityPolicyViolationEvent` if the website set in the fetch API (line 6) redirects to a website other than `https://example.org`:

{{< highlight html "linenos=table,linenostart=1" >}}
<!-- Set the Content-Security-Policy to only allow example.org -->
<meta http-equiv="Content-Security-Policy"
      content="connect-src https://example.org">
<script>
// Listen for a CSP violation event
document.addEventListener('securitypolicyviolation', () => {
  console.log("Detected a redirect to somewhere other than example.org");
});
// Try to fetch example.org. If it redirects to another cross-site website
// it will trigger a CSP violation event
fetch('https://example.org/might_redirect', {
  mode: 'no-cors',
  credentials: 'include'
});
</script>
{{< / highlight >}}

When the redirect of interest is cross-site and conditioned on the presence of a cookie marked `SameSite=Lax`, the approach outlined above won't work, because `fetch` doesn't count as a top-level navigation. In a case like this, the attacker can use another CSP directive, [`form-action`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/form-action), and leverage the fact that submitting a HTML form using `GET` as its method does count as a top-level navigation.

The example below triggers a `SecurityPolicyViolationEvent` if the form's action (line 3) redirects to a website other than `https://example.org`:

{{< highlight html "linenos=table,linenostart=1" >}}
<!-- Set the Content-Security-Policy to only allow example.org -->
<meta http-equiv="Content-Security-Policy"
      content="form-action https://example.org">
<form action="https://example.org/might_redirect"></form>
<script>
// Listen for a CSP violation event
document.addEventListener('securitypolicyviolation', () => {
  console.log("Detected a redirect to somewhere other than example.org");
});
// Try to get example.org via a form. If it redirects to another cross-site website
// it will trigger a CSP violation event
document.forms[0].submit();
</script>
{{< / highlight >}}

Note that this approach is unviable in Firefox (contrary to Chromium-based browsers) because `form-action` doesn't block redirects after a form submission in that browser.

## Case Scenarios

An online bank decides to redirect wealthy users to attractive stock opportunities by triggering a navigation to a reserved space on the website when these users consult their account balance. If this is only done for a specific group of users, it becomes possible for an attacker to leak the "client status" of the user.

## Partitioned HTTP Cache Bypass

If a site `example.com` includes a resource from `*.example.com/resource` then that resource will have the same caching key as if the resource was directly requested through top-level navigation. That is because the caching key is consisted of top-level *eTLD+1* and frame *eTLD+1*. [^cache-bypass]

Because a window can prevent a navigation to a different origin with `window.stop()` and the on-device cache is faster than the network,
it can detect if a resource is cached by checking if the origin changed before the `stop()` could be run. 

```javascript
async function ifCached_window(url) {
  return new Promise(resolve => {
    checker.location = url;

    // Cache only
    setTimeout(() => {
      checker.stop();
    }, 20);

    // Get result
    setTimeout(() => {
      try {
        let origin = checker.origin;
        // Origin has not changed before timeout.
        resolve(false);
      } catch {
        // Origin has changed.
        resolve(true);
        checker.location = "about:blank";
      }
    }, 50);
  });
}
```
Create window (makes it possible to go back after a successful check)
```javascript
let checker = window.open("about:blank");
```
Usage
```javascript
await ifCached_window("https://example.org");
```
{{< hint info >}}
Partitioned HTTP Cache Bypass can be prevented using the header `Vary: Sec-Fetch-Site` as that splits the cache by its initiator, see [Cache Protections]({{< ref "/docs/defenses/design-protections/cache-protections.md" >}}). It works because the attack only applies for the resources from the same site, hence `Sec-Fetch-Site` header will be `cross-site` for the attacker compared to `same-site` or `same-origin` for the website.
{{< /hint >}}

## Defense

|       Attack Alternative        | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :-----------------------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
|   *history.length* (iframes)    |                                         ✔️                                          |                          ❌                          |                                 ✔️                                 |                                        [FIP]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}})                                         |
|   *history.length* (windows)    |                                         ❌                                          |                          ✔️                          |                                 ❌                                 |                                       [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}})                                       |
| *onload* event inside an iframe |                                         ✔️                                          |                          ❌                          |                                 ✔️                                 |                                        [FIP]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}})                                         |
|          Download bar           |                                         ✔️                                          |                          ❌                          |                  ❌{{< katex>}}^{1}{{< /katex >}}                  |                                       [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}})                                       |
|  Download Navigation (iframes)  |                                         ✔️                                          |                          ❌                          |                  ❌{{< katex>}}^{1}{{< /katex >}}                  |                                        [FIP]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}})                                         |
|  Download Navigation (windows)  |                                         ❌                                          |           ❌{{< katex>}}^{1}{{< /katex >}}           |                                 ❌                                 |                                       [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}})                                       |
|  Inflation (Server-Side Errors) |                                         ✔️                                          |                          ❌                          |                                 ❌                                 |                                        [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}})                                        |
|  Inflation (Client-Side Errors) |                                         ❌                                          |                          ❌                          |                                 ❌                                 |                                       [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}})                                       |
|         CSP Violations          |            ❌{{< katex>}}^{2}{{< /katex >}}                                        |                          ❌                          |                                 ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |

🔗 – Defense mechanisms must be combined to be effective against different scenarios.

____
1. Neither [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) nor [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) helps with the mitigation of the redirect leaks because when the header `Content-Disposition` is present, other headers are being ignored.
2. SameSite cookies in Lax mode could protect against iframing a website, but won't help with the leaks through window references or involving server-side redirects, in contrast to Strict mode.

## Real-World Examples

A vulnerability reported to Twitter used this technique to leak the contents of private tweets using [XS-Search]({{< ref "../attacks/xs-search.md" >}}). This attack was possible because the page would only trigger a navigation if there were results to the user query [^1].

## References

[^1]: Protected tweets exposure through the url, [link](https://hackerone.com/reports/491473)
[^2]: Disclose domain of redirect destination taking advantage of CSP, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=313737)
[^3]: Using Content-Security-Policy for Evil, [link](http://homakov.blogspot.com/2014/01/using-content-security-policy-for-evil.html)
[^cache-bypass]: [github.com/xsleaks/wiki/pull/106](https://github.com/xsleaks/wiki/pull/106)
[^spec-redirects]: HTTP-redirect fetch, [link](https://fetch.spec.whatwg.org/#http-redirect-fetch)
[^redirect-leak]: XS-Leaks in redirect flows, [link](https://docs.google.com/presentation/d/1rlnxXUYHY9CHgCMckZsCGH4VopLo4DYMvAcOltma0og)
[^chrome-maxlength]: Chromium Docs - Guidelines for URL Display, [link](https://chromium.googlesource.com/chromium/src/+/main/docs/security/url_display_guidelines/url_display_guidelines.md)
