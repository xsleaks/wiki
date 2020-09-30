+++
title = "Partitioned HTTP Cache"
description = ""
date = "2020-07-21"
category = "defenses"
menu = "main"
+++

## Explanation

[Cache probing attacks](https://TODO) have been present on the web for a long time mainly because browsers HTTP cache is shared across all the websites visited by a user, allowing attackers to interact with it and infer private information from other origins.

<!--TODO(manuelvsousa): Add reference to cache probing attacks-->

Considering Opt-in solutions, applications can use the [`Vary` Header combined with `Fetch-Metadata`](https://TODO) to prevent cross-origin fetches to be affected by the site cache or use [some workarounds]({{< ref "../design-protections/subresource-protections.md" >}}) to protect resources, which come with downsides. Browsers, however, have been planning a defense mechanism to segregate the cached resources per origin/site, making it impossible for attackers pages to interact with cached contents of different origins[^1] [^2] [^3]. Specifically, browsers tested caching with finer-grained keys such as the tuple <`top-frame origin`, URL> or even triples of values.

{{< hint good >}}
Chrome [^1] and Firefox [^2] are still discussing an implementation for a Multi-Keyed Cache while Safari [^3] already runs a version of it since 2013.

[^1]: Split Disk Cache Meta Bug (Blink), [link](https://bugs.chromium.org/p/chromium/issues/detail?id=910708)
[^2]: Top-level site partitioning (Gecko), [link](https://bugzilla.mozilla.org/show_bug.cgi?id=1590107)
[^3]: Optionally partition cache to prevent using cache for tracking (Webkit), [link](https://bugs.webkit.org/show_bug.cgi?id=110269)
{{< /hint >}}


## Relevant Projects

### Intelligent Tracking Prevention

[Intelligent Tracking Prevention](https://webkit.org/tracking-prevention/) (ITP) is a privacy feature part of WebKit. It's a conjunction of several tracking prevention features containing a partitioned HTTP Cache which originated from the initial [WebKit Keyed cache from 2013](https://bugs.webkit.org/show_bug.cgi?id=110269). The used keys for the cache are the top frame's eTLD+1 and the origin of each fetched subresource.

{{< hint warning >}}
Researchers found out attackers [could abuse some ITP features](https://TODO) to infer private user data.
{{< /hint >}}

<!--TODO(manuelvsousa): ADD ITP XS-Leak here -->

### First Party Isolation

First Party Isolation is a [Browser Extension](https://addons.mozilla.org/en-US/firefox/addon/first-party-isolation/) for Firefox which restricts access to cookies and persistent data (e.g cache) per domain.

## Considerations

Partitioned HTTP caches are a promising security feature that will eventually land in browsers. These partitioning strategies will mitigate all the XS-Leaks leveraging browsers caches and might be extended to other browser resources which help mitigate resilient attack vectors like the [Socket Exhaustion XS-Leak](https://TODO).

<!--TODO(manuelvsousa): Add socket exhaustion ?-->

## References

[^1]: Double-keyed HTTP cache, [link](https://github.com/whatwg/fetch/issues/904)
[^2]: Explainer - Partition the HTTP Cache, [link](https://github.com/shivanigithub/http-cache-partitioning)
[^3]: Client-Side Storage Partitioning, [link](https://privacycg.github.io/storage-partitioning/)
