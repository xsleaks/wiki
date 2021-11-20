+++
title = "Error Events"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "Error Events",
    "Status Code",
    "nosniff",
    "Content-Type",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
]
menu = "main"
weight = 2
+++


When a webpage issues a request to a server (e.g. fetch, HTML tags), the server receives and processes this request. When received, the server decides whether the request should succeed (e.g. 200) or fail (e.g. 404) based on the provided context. When a response has an error status, an [error event](https://developer.mozilla.org/en-US/docs/Web/API/Element/error_event) is fired by the browser for the page to handle. These errors also cover situations where the parser fails, for example when trying to embed `HTML` content as an image.

For example, attackers can detect whether a user is logged in to a service by checking if the user has access to resources only available to authenticated users [^3]. The impact of this XS-Leak varies depending on the application, but it can lead to sophisticated attacks with the ability to deanonymize users [^1].

Error events can be thrown from a large variety of HTML tags, and some behaviors vary from browser to browser [^4]. For instance, the behavior can depend on the loaded resources, HTML tags, presence of certain headers (e.g. `nosniff`, `Content-Type`), or the enforcement of default browser protections, etc.

The principle of leaking information with error events can be abstracted and applied to a variety of XS-Leaks. For example, one technique for [Cache Probing]({{< ref "cache-probing.md" >}}) uses Error Events to detect if a certain image was cached by the browser. [^demo]

## Code Snippet
The below snippet demonstrates how an Error Event can be detected with the `<script>` tag:

```javascript
function probeError(url) {
  let script = document.createElement('script');
  script.src = url;
  script.onload = () => console.log('Onload event triggered');
  script.onerror = () => console.log('Error event triggered');
  document.head.appendChild(script);
}
// because google.com/404 returns HTTP 404, the script triggers error event
probeError('https://google.com/404');

// because google.com returns HTTP 200, the script triggers onload event
probeError('https://google.com/');
```

## Defense

The mitigation of this XS-Leak often varies depending on how applications handle certain resources. The general approach is to adopt consistent behaviors whereever possible. In specific scenarios, applications might use [Subresource Protections]({{< ref "/docs/defenses/design-protections/subresource-protections.md" >}}) to prevent attackers from predicting a URL and going forward with an attack.

Finally, without applying bigger changes in the logic of applications, generic web platform security features can be deployed to mitigate this XS-Leak at a larger scale.

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ✔️                                          |                          ❌                          |                                 ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) {{< katex>}}^{1}{{< /katex >}} |

____

1. The resource isolation policy should be enough to prevent error-based cross-site leaks, although in some scenarios without the [Framing Isolation Policy]({{< ref "/docs/defenses/isolation-policies/framing-isolation" >}}), the error events could be leaked through iframes.

## Real World Example

A bug allowed abusing a Twitter API endpoint to which only a specified user would have access. This endpoint would return an error to every Twitter user except the owner. An attacker could exploit this behavior to deanonymize a user [^3]. Similarly, another bug allowed abusing an image authentication mechanism of private messages to achieve the same goal  [^2] [^1].

## References

[^1]: Leaky Images: Targeted Privacy Attacks in the Web, [link](https://www.usenix.org/system/files/sec19fall_staicu_prepub.pdf)
[^2]: Tracking of users on third-party websites using the Twitter cookie, due to a flaw in authenticating image requests, [link](https://hackerone.com/reports/329957)
[^3]: Twitter ID exposure via error-based side-channel attack, [link](https://hackerone.com/reports/505424)
[^4]: Cross-Origin State Inference (COSI) Attacks: Leaking Web Site States through XS-Leaks, [link](https://arxiv.org/pdf/1908.02204.pdf) (see page 6)
[^demo]: Detect errors with onload/onerror with script, [link](https://xsinator.com/testing.html#Event%20Handler%20Leak%20(Script))
