---
title: Introduction
type: docs
bookToc: false
---

# XS-Leaks Wiki
## Overview

Cross-Site Leaks (XS-Leaks, XSLeaks) are a class of vulnerabilities derived from browser side-channel techniques [^side-channel]. These are similar to Cross-Site Request Forgery [^csrf] (CSRF) techniques but instead of allowing other websites to take actions on behalf of a user, they can be used to infer information about them. This is done by exploiting a variety of features built into browsers [^browser-features].


## The principle of an XS-Leak

Websites interacting with each other is core to the behavior of the web. Browsers provide a wide variety of interfaces for such interaction between different web applications. These interfaces have different security measures built on top to try to constrain websites behavior (e.g. the [Same-Origin Policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)). XS-Leaks take advantage of small pieces of information that can leak during these interactions in order to infer sensitive information about users such as their data on other websites, operating systems they use or internal networks they are connected to. 

These pieces of information usually have a binary form and are called oracles. These oracles usually answer with *YES* and *NO* to cleverly prepared questions. For example, such an oracle could be asked:

> Does the word *secret* exist in the search results?

In a vulnerable application, the above question might be equivalent to asking:

> Does the query *?query=secret* return *HTTP200* status code?"

Since it is possible to detect *HTTP200* status code with [Error Events]({{< ref "./docs/attacks/error-events.md" >}}), this has the same effect as asking:

> Does the query *?query=secret* trigger the *onload* event?

The above query could be repeated by an attacker for many different keywords and with that, used to infer sensitive information about the user from a cross-site website.

Browsers provide a wide range of different APIs that, while well-intended, can end up leaking small amounts of cross-origin information.

## Example

Websites are generally not allowed to access data on other websites. For example, *evil.com* is forbidden from explicitly reading a response from *bank.com*, but *evil.com* can attempt to load a script from *bank.com* since that was originally seen as harmless [^harmless]. However, *evil.com* can also determine whether or not the script successfully loaded.

{{< hint info >}}

Suppose that *bank.com* has an API endpoint that returns data about a user's receipt for a given query.

1. The page *evil.com* could attempt to load the URL *bank.com/my_receipt?q=groceries* as a script, since by default, the browser will attach cookies with the request.
2. If the user has recently bought groceries, it will load successfully (because of the HTTP200 status code).
3. But if they haven't, it will trigger an [Error Event]({{< ref "./docs/attacks/error-events.md" >}}) (because of the HTTP404 status code) that *evil.com* can listen for.
4. By repeating this with different queries, the attacker could infer a significant amount of information about the user's transaction history.
{{< /hint >}}

In the above example, two websites on two different origins (*evil.com* and *bank.com*) were interacting through an API that browsers allow websites to use. None of this exploited a bug in the browser or a bug in *bank.com*. But nonetheless, it allowed *evil.com* to gain some small amount of information about the user on *bank.com*.  



## Root Cause of XS-Leaks

The root cause of most XS-Leaks is inherent to the design of the web. Oftentimes applications are vulnerable to some cross-site information leaks without having done anything wrong. It is challenging to fix the root cause of XS-Leaks at the browser level because in many cases doing so would break existing websites so browsers are implementing various [Defense Mechanisms]({{< ref "defenses" >}}) to overcome these difficulties. Many of these defenses are used via websites opting into a more restrictive security model, usually through certain HTTP headers (e.g. *[Cross-Origin-Opener-Policy]({{< ref "./docs/defenses/opt-in/coop.md">}}): same-origin*), which often must be combined to achieve the desired outcome.

We can distinguish different sources of XS-Leaks, such as:

1. Browser APIs (e.g. [Frame Counting]({{< ref "frame-counting.md" >}}) and [Timing Attacks]({{< ref "timing-attacks.md" >}}))
2. Browser Implementation Details and Bugs (e.g. [Connection Pooling]({{< ref "./docs/attacks/timing-attacks/connection-pool.md" >}}) and [typeMustMatch]({{< ref "./docs/attacks/historical/content-type.md#typemustmatch" >}}))
3. Hardware Bugs (e.g. Speculative Execution Attacks [^spectre])

## A little bit of history

XS-Leaks have always been part of the web platform but have gained attention in recent years [^old-wiki] as new techniques were found to increase their impact. [Timing attacks]({{< ref "network-timing.md" >}}) against the web have been discussed since at least [2000](https://dl.acm.org/doi/10.1145/352600.352606). In 2015 Gelernter and Herzberg published "Cross-Site Search Attacks" [^xs-search-first] which covered their work on exploiting timing attacks to implement high impact XS-Search attacks against Google and Microsoft. From there, more XS-Leak techniques were discovered and tested over time. Recently, browsers have been working on developing a variety of new standards that will make it easier to defend applications against XS-Leaks.

## About the wiki

This wiki is meant to both introduce readers to XS-Leaks and serve as a reference guide to experienced researchers exploiting XS-Leaks. While this wiki contains information on many different techniques, new techniques are always emerging. Improvements, whether to add new techniques or expand existing pages, are always appreciated!

## References
[^side-channel]: Side Channel Vulnerabilities on the Web - Detection and Prevention, [link](https://owasp.org/www-pdf-archive/Side_Channel_Vulnerabilities.pdf)  
[^csrf]: Cross Site Request Forgery (CSRF), [link](https://owasp.org/www-community/attacks/csrf)  
[^browser-features]: These features might be maintained to preserve backwards compatibility, though, sometimes new features are added to browsers regardless of the introduction of potential cross-site leaks (e.g. [Scroll to Text Fragment]({{< ref "scroll-to-text-fragment.md" >}})) as the benefits are considered to outweigh the downsides.  
[^harmless]: Websites being able to interact and include resources from each other is a key part of how the web works. For example, many websites allow users to post content that includes images embedded from elsewhere on the web. Fundamentally, this is an intended behavior of the web. But, over time the downsides of this sort of interaction have become better understood.  
[^old-wiki]: Browser Side Channels, [link](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels)  
[^xs-search-first]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)  
[^spectre]: Meltdown and Spectre, [link](https://spectreattack.com/)  
