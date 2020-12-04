---
title: "Isolation Policies"
weight: 3
---

# Isolation Policies

This section describes proposed defenses against different kinds of cross-site interactions, presented in the form of _isolation policies_:

* To defend against cross-site requests for common resources (e.g. scripts, images, fetch) with [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}), check [Resource Isolation Policy]({{< ref "./resource-isolation.md" >}}).
* To defend against cross-site framing with [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}), check [Framing Isolation Policy]({{< ref "./framing-isolation.md" >}}).
* To defend against cross-site navigational requests with [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}), check [Navigation Isolation Policy]({{< ref "./navigation-isolation.md" >}}).
* To defend against all cross-site interactions with either [Fetch Metadata]({{< ref "../opt-in/fetch-metadata.md">}}), [SameSite cookies]({{< ref "../opt-in/same-site-cookies">}}), or the Referer header, check [Strict Isolation Policy]({{< ref "./strict-isolation.md" >}}).

