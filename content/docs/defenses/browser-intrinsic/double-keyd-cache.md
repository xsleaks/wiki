+++
title = "Double Keyed Cache"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++

## Explanation



https://bugzilla.mozilla.org/show_bug.cgi?id=1590107
https://bugs.chromium.org/p/chromium/issues/detail?id=910708

<!--TODO(manuelvsousa): Add reference to cache probing attacks and maybe socket exhaustion-->


## Relevant Projects

### Intelligent Tracking Prevention




WebKit
https://webkit.org/tracking-prevention/
https://bugs.webkit.org/show_bug.cgi?id=110269


{{< hint warning >}}
A number of XS-Leaks resulted in browser features triggering changes in fundamental web platform features. An [XS-Leak abused ITP features](https://TODO) which could lead to private information leaks.
{{< /hint >}}

<!--TODO(manuelvsousa): ADD ITP XS-Leak here -->

### First Party Isolation

Mozilla

https://addons.mozilla.org/en-US/firefox/addon/first-party-isolation/

## Considerations


## XS-Leaks Mitigation Overview

|                           XS-Leak                                 | Double Keyd Cache Mitigation |  Full Mitigation   |
|:-----------------------------------------------------------------:|:----------------------------:|:-------------------:
| [Frame Counting]({{< ref "../../attacks/frame-counting.md" >}})   |         ❌                   |         ❌
| [Navigations]({{< ref "../../attacks/navigations.md" >}})         |         ❌                   |         ❌
| [ID Leaks]({{< ref "../../attacks/id-attribute.md" >}})           |         ❌                   |         ❌

### Table Caption

- **Double Keyd Cache Protection** - At least one attack alternative of the XS-Leak can be mitigated if the mechanism would exist
- **Full Mitigation** - Fully mitigates an XS-Leak if the mechanism would exist


[^1]: Double-keyed HTTP cache, [link](https://github.com/whatwg/fetch/issues/904)
[^2]: Explainer - Partition the HTTP Cache, [link](https://github.com/shivanigithub/http-cache-partitioning)
[^3]: Client-Side Storage Partitioning, [link](https://privacycg.github.io/storage-partitioning/)





