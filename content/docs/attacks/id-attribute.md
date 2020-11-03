+++
title = "ID Attribute"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "onblur",
    "focus",
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "Framing Protections",
]
menu = "main"
weight = 3
+++


The `id` attribute is widely used to identify some `HTML` elements. Unfortunately, cross-origin websites can determine whether a given `id` is set anywhere on the page by leveraging the `focus` event and `URL` fragments. If `https://example.com/foo#bar` is loaded, the browser will attempt to scroll to the element with `id="bar"`. This can be detected cross-origin by loading `https://example.com/foo#bar` in an iframe and if there is an element with `id="bar"` then the `focus` event will fire. The `blur` event can also be used for the same purpose [^1]. 

Some web applications set `id` attributes on `focusable` elements that may lead to user information disclosure. These `ids` can either be direct information related to the user (e.g. a secret) or information associated with a user state (e.g account status). 

## Case Scenarios

- A bank allows its clients to generate short numeric One-Time PINs (OTP) in the browser application to authenticate sessions on mobile devices. The bank used the OTP as the `id` of a `button` that is used to show the PIN to the client. This technique could be used to steal these OTP codes by brute forcing every option and then using them to compromise user accounts.
- A web application uses a specific set of predefined `ids` and `HTML` elements when an account has a premium status or the user is of a certain gender. The attacker can detect whether a specific `id` is present on the victim's page and leak the account information.


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |

## References

[^1]: Leaking IDs using focus, [link](https://portswigger.net/research/xs-leak-leaking-ids-using-focus)