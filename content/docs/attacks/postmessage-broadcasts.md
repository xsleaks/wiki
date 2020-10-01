+++
title = "postMessage Broadcasts"
description = ""
date = "2020-07-06"
category = [
    "Attack",
]
defenses = [
    "Application Fix",
]
menu = "main"
weight = 3
+++

Applications often use [postMessage broadcasts](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) to provide information to any interested origin. Apart from the obvious security issues (providing sensitive information to **any** origin), other problems might occur if a legitimate postMessage broadcast is not properly implemented [^1].

If a broadcast happens based on user information, attackers might be able to leak information if they can distinguish two scenarios. There are multiple ways applications can be inconsistent with broadcast:

- An application sends different broadcasted messages.
- An application sends a broadcast or no broadcast.

## Defense

There is no clear solution to mitigate this XS-Leak as it depends deeply on the purpose of doing a postMessage broadcast. Applications should limit postMessage communications to a group of known origins and, when this is not possible, they should have the same behavior even when in different states to prevent attackers from inferring any differences.

## References

[^1]: Cross-Origin State Inference (COSI) Attacks: Leaking Web Site States through XS-Leaks, [link](https://arxiv.org/pdf/1908.02204.pdf)