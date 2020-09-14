+++
title = "Subresource Protections"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++


The principle of protecting subresources is the same as protecting endpoints from [CSRF attacks](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html). The difference from CSRF protections is that in the case of XS-Leaks, only GET requests are the ones worth protecting. To apply this protection, applications can append a (cryptographically strong) pseudorandom value, unique to each request/session, to make the URL of a subresource unpredictable to an attacker.

## Deployment

This protection might work in some scenarios but it has some disadvantages:

- Hard to deploy as it requires substantial changes in the codebase. It might break the desired behavior of the feature.
- It will break bookmarks and other permanent references.

