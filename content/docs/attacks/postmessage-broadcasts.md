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




## Defense

There is no clear solution to mitigate this XS-Leak, as it depends deeply on the purpose of doing a postMessage broadcast. Applications should limit postMessage communications to a group of known origins and when this is not possible, they should standardize the same behavior across different states to prevent attacks from inferring a distinction from two different requests.

## References

[^1]: Cross-Origin State Inference (COSI) Attacks: Leaking Web Site States through XS-Leaks, [link](https://arxiv.org/pdf/1908.02204.pdf)
