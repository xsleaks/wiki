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
+++


When a website triggers a request to a server, this request will be received and processed by that server. When received it will decide whether the request should succeed (e.g 200 OK) or fail (e.g 404 NOT FOUND) based on the provided context. When a request fails an [error event](https://developer.mozilla.org/en-US/docs/Web/API/Element/error_event) will be fired and handled by the application when necessary, with a proper listener. This often occurs when embedding resources in `image`, `script` and other tags or generic requests (fetch). These errors are also extended to situations where the parser fails, for example, trying to embed `HTML` content as an image.

The misuse of errors connected with user information, can introduce information leaks since attackers are able distinguish whether a cross-site request fails or succeeds. For example, attackers can detect whether a user is logged into a service by checking if the user has access to resources only available to authenticated users [^3]. The main principle of an Error Events Attack is checking if a user has access to a specific resource [^3]. The impact of this alternative may vary depending on the application but it often leads to sophisticated attacks [^1].

The principle of leaking information with error events can be abstracted and applied with a variety XS-Leaks. For example, one of [Cache Probing](https://TODO) alternatives use error events to detect if a certain image was cached by the browser.

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| iframe                             |         ✔️                 |      ✔️         |  ❌   |          ✔️         |


## Real World Example

1. A bug reported to Twitter abused the sharing photos mechanism [^2] [^1]
2. asdsda [^3]

## References

[^1]: Leaky Images: Targeted Privacy Attacks in the Web, [link](https://www.usenix.org/system/files/sec19fall_staicu_prepub.pdf)
[^2]: Tracking of users on third-party websites using the Twitter cookie, due to a flaw in authenticating image requests, [link](https://hackerone.com/reports/329957)
[^3]: Twitter ID exposure via error-based side-channel attack, [link](https://hackerone.com/reports/505424)