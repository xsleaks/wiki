+++
title = "Error Events"
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
weight = 2
+++


When a webpage issues a request to a server (e.g fetch, HTML tags), this request will be received and processed by that server. When received the server will decide whether the request should succeed (e.g 200) or fail (e.g 404) based on the provided context. When a response has an error status an [error event](https://developer.mozilla.org/en-US/docs/Web/API/Element/error_event) will be fired by the browser for the page to handle. These errors are also extended to situations where the parser fails, for example, trying to embed `HTML` content as an image. 

For example, attackers can detect whether a user is logged into a service by checking if the user has access to resources only available to authenticated users [^3]. The impact of this XS-Leak varies depending on the application but it can lead to sophisticated attacks with the ability to deanonymize users [^1].

Error events can be thrown from a large variety of HTML tags, and some behaviors vary from browser to browser [^4]{{< katex>}}^{p. 6}{{< /katex >}}. For instance, they depend on the loaded resources, HTML tags, presence of certain headers (e.g `nosniff`, `Content-Type`) or enforcement of default browser protections, etc.

The principle of leaking information with error events can be abstracted and applied to a variety of XS-Leaks. For example one of [Cache Probing](https://TODO) alternatives use Error Events to detect if a certain image was cached by the browser.

## Defense

The mitigation of this XS-Leak often varies on how applications handle certain resources and ends in the adoption of consistent behaviors as much as possible. In specific scenarios, applications might use [Subresource Protections](https://TODO) to prevent attackers from predicting an URL and go forward with an attack. 

Finally, without applying bigger changes in the logic of applications, generic web platform security features could be deployed to mitigate this XS-Leak at a larger scale.

| [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:--------------------------:|:---------------:|:-----:|:--------------------:|
|         ✔️{{< katex>}}^{🔗}{{< /katex >}}                 |      ✔️        |  ❌   |          ✔️{{< katex>}}^{🔗}{{< /katex >}}          |

🔗 - Defense mechanisms must be combined to be effective against different scenarios.

## Real World Example

1. A bug abused a Twitter API endpoint where only a specified user would have access to it. This endpoint would cause an error to every Twitter user but the owner. An attacker could exploit this difference to deanonymize a user with one of its pages [^3]. Similarly, another bug abused an image authentication mechanism of private messages to achieve the same goal  [^2] [^1].

## References

[^1]: Leaky Images: Targeted Privacy Attacks in the Web, [link](https://www.usenix.org/system/files/sec19fall_staicu_prepub.pdf)
[^2]: Tracking of users on third-party websites using the Twitter cookie, due to a flaw in authenticating image requests, [link](https://hackerone.com/reports/329957)
[^3]: Twitter ID exposure via error-based side-channel attack, [link](https://hackerone.com/reports/505424)
[^4]: Cross-Origin State Inference (COSI) Attacks: Leaking Web Site States through XS-Leaks, [link](https://arxiv.org/pdf/1908.02204.pdf)
