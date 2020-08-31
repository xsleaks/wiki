+++
title = "Execution Timing"
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

Measuring the time of JavaScript execution in a browser can be give attackers information when certain events are triggered, and how long some operations take. 

## CSS Selectors

### jQuery & Short-circuiting

Attackers can abuse CSS selectors and `short-circuit` evaluation of expressions, received in an `URL` hash and evaluated by JQuery (`jQuery(location.hash)`)[^3].

The following selector will spend more time running if a `main` tag with `id='site-main'` exists:

```javascript
$("*:has(*:has(*:has(*)) *:has(*:has(*:has(*))) *:has(*:has(*:has(*)))) main[id='site-main']")
```

This delay happens as the expression is compared from right to left, so if the first selector does not exist and fails to evaluate (`main[id='site-main']`), the rest of the selector which increases timing on purpose, is ignored. This attack can be used to steal secrets from a page like CSRF tokens.

{{< hint warning >}}
This attack is no longer possible in Browsers with process isolation mechanisms in place. This mechanism is only present in Chromium-Based browsers with [Site Isolation](https://www.chromium.org/Home/chromium-security/site-isolation) and *soon* in Firefox with [Project Fission](https://wiki.mozilla.org/Project_Fission).
{{< /hint >}}

{{< hint info >}}
[Service Workers]({{< ref "execution-timing.md#service-workers-1" >}})  can be abused to 
{{< /hint >}}

### CSS & IntersectionObserver
dsadsasaddasdsasad [^4]


## JavaScript Execution

### Service Workers

dsadsasaddasdsasad [^3]

### Service Workers

dasdsadasads [^5]

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../../defenses/opt-in/fetch-metadata.md" >}})  | [Cross-Origin-Opener-Policy]({{< ref "../../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| portal              |         ✔️         |      ✔️         |  ❌   |          ❌         |

{{< hint warning >}}
The [`portal`](https://web.dev/hands-on-portals/) element is only available on Chromium-based browsers under a preference flag. The corresponding specification is still under active discussion.
{{< /hint >}}


[^1]: Loophole: Timing Attacks on Shared Event Loops in Chrome, [link](https://www.usenix.org/system/files/conference/usenixsecurity17/sec17-vila.pdf)
[^2]: Matryoshka - Web Application Timing Attacks (or.. Timing Attacks against JavaScript Applications in Browsers), [link](https://sirdarckcat.blogspot.com/2014/05/matryoshka-web-application-timing.html)
[^3]: A timing attack with CSS selectors and Javascript, [link](https://blog.sheddow.xyz/css-timing-attack/)
[^4]: Restrictions for scroll-to-CSS-selector (PUBLIC), [link](https://docs.google.com/document/d/15HVLD6nddA0OaI8Dd0ayBP2jlGw5JpRD-njAyY1oNZo/edit#heading=h.wds2qckm3kh5)
[^5]: Security: XS-Search + XSS Auditor = Not Cool, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=922829)

