+++
title = "Compression leaks"
description = ""
date = "2020-10-08"
category = [
    "Attack",
]
abuse = [
    "Window References",
]
defenses = [
    "Cross-Origin-Opener-Policy",
    "SameSite Cookies"
]
menu = "main"
weight = 2
+++
When compression is used and theres reflected input such as from the URL or chat messages it maybe possible to leak the contents of a website.
This is because when content is repeated it is more compressible which results in a smaler download that is detectable using timing attacks such as using the window "unload" event.
The benfit of this is that the size change is controled by the attacker so the server can always respond with same responce.

## HIST
An example of this attack is [HIST](https://www.blackhat.com/docs/us-16/materials/us-16-VanGoethem-HEIST-HTTP-Encrypted-Information-Can-Be-Stolen-Through-TCP-Windows.pdf)
The primany defense for this attack is to disable third-party cookies however window navgation timings are not affected by this.
So also setting a Cross-Origin-Opener-Policy may make this attack harder since it prevents a window from being reused after a navagtion away has been completed.

## BREACH
An example of this attack for leaking HTTPS traffic is [BREACH](http://breachattack.com/resources/BREACH%20-%20SSL,%20gone%20in%2030%20seconds.pdf) this uses a network sniffer to get the response size.

## CRIME
Migrated for SPDY and TLS-level compression.

## Projects
There are multiple tools to exploit this behaviour such as [decrypto-org/rupture](https://github.com/decrypto-org/rupture) and
[nealharris/BREACH](https://github.com/nealharris/BREACH)

## Defenses
- Disable compression
- Using seprate requests for secrets prevents an attacker from injecting data.
- SameSite cookies prevents cookies being sent in cross origin elements.
- Cross-Origin-Opener-Policy prevents window reuse for timing attacks.
