+++
title = "Cache Probing"
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



### Cache Probing with Error Events

## Defense

| Attack                 | [Paritioned Cache](https://TODO)   | [Vary: Sec-Fetch-Site]({{< ref "../defenses/opt-in/fetch-metadata.md#fetch-metadata--cache-probing-attacks" >}})  | [Subresource Protections]({{< ref "../defenses/design-protections/subresource-protections.md" >}}) |
|:----------------------:|:---------------------------------:|:-------------------------------------:|:---------------------------------------:|
| Cache Probing          |                ✔️                 |                  ✔️                   |                   ✔️                   | 

## Real World Example

1.  [^3]

## References

[^1]: Abusing HTTP Status Codes to Expose Private Information, [link](https://www.grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information)
[^2]: HTTP Cache Cross-Site Leaks, [link](http://sirdarckcat.blogspot.com/2019/03/http-cache-cross-site-leaks.html)
[^3]: Mass XS-Search using Cache Attack, [link](https://terjanq.github.io/Bug-Bounty/Google/cache-attack-06jd2d2mz2r0/index.html)