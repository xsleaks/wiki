+++
title = "postMessage Broadcasts"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "postMessage",
]
defenses = [
    "Application Fix",
]
menu = "main"
weight = 3
+++

Applications often use [postMessage broadcasts](https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage) to share information with other origins. Using `postMessage` can lead to two kinds of XS-Leaks:

* Sharing sensitive messages with untrusted origins
    * The `postMessage` API supports a `targetOrigin` parameter that can be used to restrict which origins can receive the message. If the message contains any sensitive data, it is important to use this parameter.  

* Leaking information based on varying content or on the presence of a broadcast
    * Similar to other XS-Leak techniques, this could be used to form an oracle. For example, if an application sends a postMessage broadcast saying "Page Loaded" only if a user  with a given username exists, this could be used to leak information. 

## Defense

There is no clear solution to mitigate this XS-Leak as it depends deeply on the purpose of sending a postMessage broadcast. Applications should limit postMessage communications to a group of known origins, and, when this is not possible, the communications should behave consistently regardless of the state to prevent attackers from inferring any differences.

## References

[^1]: Cross-Origin State Inference (COSI) Attacks: Leaking Web Site States through XS-Leaks, [link](https://arxiv.org/pdf/1908.02204.pdf)
