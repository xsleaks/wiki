+++
title = "postMessage Broadcasts"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "",
    "",
]
menu = "main"
+++


Applications often use [postMessage broadcasts](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) to provide information to any interested origin. Apart from the obvious security issues (providing sensitive information to **any** origin) other problems might occur if a legitimate postMessage broadcast is not properly deployed [^1].

If a broadcast happens based on user information, attackers might be able to leak that information if they can distinguish requests. There are multiple ways applications can be inconsistent with broadcasts based on user information:

- An application send different broadcasted messages.
- An application sends a broadcast, or no broadcast.

## Defense

There is no clear solution to mitigate this XS-Leak, as it depends deeply on the purpose of doing a postMessage broadcast. Applications should limit postMessage communications to a group of known origins and when this is not possible, they should standardize the same behavior across different states to prevent attacks from inferring a distinction from two different requests.

## References

[^1]: Cross-Origin State Inference (COSI) Attacks: Leaking Web Site States through XS-Leaks, [link](https://arxiv.org/pdf/1908.02204.pdf)
