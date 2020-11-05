---
title: Introduction
type: docs
bookToc: false
---

# XS-Leaks Wiki
## Overview

Cross-Site Leaks (aka XS-Leaks, XSLeaks) are a class of vulnerabilities derived from browser side-channel techniques [^side-channel]. These techniques are similar to Cross-Site Request Forgery [^csrf] (CSRF) techniques, but instead of allowing other websites to perform actions on behalf of a user, they can be used to infer information about a user. This is done by exploiting a variety of features built in to browsers [^browser-features].


## The principle of an XS-Leak

The fact that websites interact with each other is core to the behavior of the web. Browsers provide a wide variety of interfaces to support such interactions between different web applications. These interfaces have different security measures built on top which are intended to constrain the behavior of websites (e.g. the [Same-Origin Policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)). XS-Leaks take advantage of small pieces of information that can leak during these interactions in order to infer sensitive information about users such as their data on other websites, operating systems they use, or internal networks they are connected to. 

These pieces of information usually have a binary form and are referred to as "oracles". These oracles usually answer with *YES* or *NO* to cleverly prepared questions. For example, an oracle can be asked:

> Does the word *secret* exist in the search results?

In a vulnerable application, the above question might be equivalent to asking:

> Does the query *?query=secret* return *HTTP200* status code?

Since it is possible to detect the *HTTP200* status code with [Error Events]({{< ref "./docs/attacks/error-events.md" >}}), this has the same effect as asking:

> Does the query *?query=secret* trigger the *onload* event?

The above query could be repeated by an attacker for many different keywords, and as a result the answers could be used to infer sensitive information about the user from a cross-site website.

Browsers provide a wide range of different APIs that, while well-intended, can end up leaking small amounts of cross-origin information.

## Example

Websites are generally not allowed to access data on other websites. For example, *evil.com* is forbidden from explicitly reading a response from *bank.com*, but *evil.com* can attempt to load a script from *bank.com* since that was originally judged to be harmless [^harmless]. However, *evil.com* can also determine whether or not the script successfully loaded.

{{< hint info >}}

Suppose that *bank.com* has an API endpoint that returns data about a user's receipt for a given query.

1. The page *evil.com* could attempt to load the URL *bank.com/my_receipt?q=groceries* as a script, since by default, the browser attaches cookies with the request.
2. If the user has recently bought groceries, the URL loads successfully (because of the *HTTP200* status code).
3. But if they haven't bought groceries, the URL triggers an [Error Event]({{< ref "./docs/attacks/error-events.md" >}}) (because of the *HTTP404* status code) that *evil.com* can listen for.
4. By repeating this approach with different queries, the attacker could infer a significant amount of information about the user's transaction history.
{{< /hint >}}

In the above example, two websites of two different origins (*evil.com* and *bank.com*) interacted through an API that browsers allow websites to use. None of this exploited a bug in the browser or a bug in *bank.com*. But nonetheless, it allowed *evil.com* to gain a small amount of information about the user on *bank.com*.  



## Root cause of XS-Leaks

The root cause of most XS-Leaks is inherent to the design of the web. Oftentimes applications are vulnerable to some cross-site information leaks without having done anything wrong. It is challenging to fix the root cause of XS-Leaks at the browser level because in many cases doing so would break existing websites. For this reason, browsers are now implementing various [Defense Mechanisms]({{< ref "defenses" >}}) to overcome these difficulties. Many of these defenses require websites to opt in to a more restrictive security model, usually through the use of certain HTTP headers (e.g. *[Cross-Origin-Opener-Policy]({{< ref "./docs/defenses/opt-in/coop.md">}}): same-origin*), which often must be combined to achieve the desired outcome.

We can distinguish different sources of XS-Leaks, such as:

1. Browser APIs (e.g. [Frame Counting]({{< ref "frame-counting.md" >}}) and [Timing Attacks]({{< ref "timing-attacks.md" >}}))
2. Browser implementation details and bugs (e.g. [Connection Pooling]({{< ref "./docs/attacks/timing-attacks/connection-pool.md" >}}) and [typeMustMatch]({{< ref "./docs/attacks/historical/content-type.md#typemustmatch" >}}))
3. Hardware bugs (e.g. Speculative Execution Attacks [^spectre])

## A little bit of history

XS-Leaks have always been part of the web platform, but have attracted more attention in recent years [^old-wiki] as new techniques were found to increase their impact. [Timing attacks]({{< ref "network-timing.md" >}}) against the web have been discussed since at least [2000](https://dl.acm.org/doi/10.1145/352600.352606). In 2015, Gelernter and Herzberg published "Cross-Site Search Attacks" [^xs-search-first] which covered their work on exploiting timing attacks to implement high impact XS-Search attacks against Google and Microsoft. Since then, more XS-Leak techniques have been discovered and tested. Recently, browsers have been improved to include a variety of new standards that make it easier to defend applications against XS-Leaks.

## About this wiki

This wiki is meant to both introduce readers to XS-Leaks and serve as a reference guide for experienced researchers exploiting XS-Leaks. While this wiki contains information on many different techniques, new techniques are always emerging. Improvements, whether they add new techniques or expand existing pages, are always appreciated!

### Contributors

The following users [contributed](https://github.com/xsleaks/wiki/graphs/contributors) to the XS-Leaks wiki:

* [Manuel Sousa](https://github.com/manuelvsousa) (creator of this wiki)
* [terjanq](https://github.com/terjanq)
* [Roberto Clapis](https://github.com/empijei)
* [David Dworken](https://github.com/ddworken)
* [NDevTK](https://github.com/NDevTK)

In addition, we would also like to acknowledge the users who [contributed](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels/_history) to the predecessor of the current XS-Leaks wiki:

* [Eduardo' Vela" \<Nava> (sirdarckcat)](https://github.com/sirdarckcat)
* [Ron Masas](https://github.com/masasron)
* [Luan Herrera](https://github.com/lbherrera)
* [Sigurd](https://github.com/DonSheddow)
* [larson reever](https://github.com/larsonreever)
* [Frederik Braun](https://github.com/mozfreddyb)
* [Masato Kinugawa](https://github.com/masatokinugawa)
* [sroettger](https://github.com/sroettger)


## References
[^side-channel]: Side Channel Vulnerabilities on the Web - Detection and Prevention, [link](https://owasp.org/www-pdf-archive/Side_Channel_Vulnerabilities.pdf)  
[^csrf]: Cross Site Request Forgery (CSRF), [link](https://owasp.org/www-community/attacks/csrf)  
[^browser-features]: In some cases, these features are maintained to preserve backwards compatibility. But, in other cases, new features are added to browsers regardless of the fact that they introduce potential Cross-Site Leaks (e.g. [Scroll to Text Fragment]({{< ref "scroll-to-text-fragment.md" >}})), as the benefits are considered to outweigh the downsides.  
[^harmless]: Websites being able to interact and include resources from each other is a key part of how the web works. For example, many websites allow users to post content that includes images embedded from elsewhere on the web. Fundamentally, this is an intended behavior of the web. But, over time, the downsides of this sort of interaction have become better understood.  
[^old-wiki]: Browser Side Channels, [link](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels)  
[^xs-search-first]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)  
[^spectre]: Meltdown and Spectre, [link](https://spectreattack.com/)  
