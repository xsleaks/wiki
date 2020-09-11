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


When a website triggers a request to a server, this request will be received and processed by that server. When received it will decide whether the request should succeed (e.g 200 OK) or fail (e.g 404 NOT FOUND) based on the provided context. When a request fail an [error event](https://developer.mozilla.org/en-US/docs/Web/API/Element/error_event) will be fired and handled by the developer when necessary, with a proper listener. This is extended to situations where

The misuse of errors connected with user information, can introduce information leaks since attackers are able distinguish whether a cross-site request fails or succeeds. For example, attackers can detect whether a user is logged into a service by checking if the user has access to resources only available to authenticated users [^3]. There are multiple alternatives to infer such behaviors:

- Checking if a user has access to a specific resource [^3]. The impact of this alternative may vary depending on the application but it often leads to sophisticated attacks [^1]
- Checking if By checking if a user has loaded a specific resource in the past (by forcing an HTTP error unless the resource is cached).

### Cache Probing

[Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md#fetch-metadata--cache-probing-attacks" >}}) 


{{< hint warning >}}
The [`portal`](https://web.dev/hands-on-portals/) element is only available on Chromium-based browsers under a preference flag. The corresponding specification is still under active discussion.
{{< /hint >}}


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| iframe                             |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (iframe)          |         ✔️                 |      ✔️         |  ❌   |          ✔️         |
| `History.length` (window.open)     |         ✔️ (If Strict)     |      ✔️         |  ✔️   |           ❌        |


## Real World Example

1. adsasd [^2]
2. asdsda [^3]

## References

[^1]: Leaky Images: Targeted Privacy Attacks in the Web, [link](https://www.usenix.org/system/files/sec19fall_staicu_prepub.pdf)
[^2]: Tracking of users on third-party websites using the Twitter cookie, due to a flaw in authenticating image requests, [link](https://hackerone.com/reports/329957)
[^3]: Twitter ID exposure via error-based side-channel attack, [link](https://hackerone.com/reports/505424)
[^4]: Abusing HTTP Status Codes to Expose Private Information, [link](https://www.grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information)