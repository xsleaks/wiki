---
title: Introduction
type: docs
bookToc: false
---

# XS-Leaks Wiki
## Overview

Cross-Site Leaks (XS-Leaks, XSLeaks) are a class of vulnerabilities derived from browser side-channel techniques [^side-channel]. These are similar to Cross-Site Request Forgery [^csrf] (CSRF, XSRF) techniques but instead of allowing other websites to take actions on behalf of a user, they can be used to infer information about the user. This is done by utilising a variety of features built into browsers. These features are often maintained to preserve backwards compatibility though sometimes new features are added to the browser despite an accepted risk of cross-site leaks [^STTF].


## The principle of an XS-Leak

Websites interacting with each other is core to the behavior of the web. Browsers provide a wide variety of interfaces for such interaction between different web applications. These interfaces have different security measures built on top to try to constrain websites behavior (e.g. the [Same-Origin Policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)). XS-Leaks take advantage of small pieces of information that can leak during these interactions in order to infer sensitive information about users. 

These pieces of information usually have a binary form and are called oracles. These oracles usually answer with *YES* and *NO* to cleverly prepared questions. For example, such an oracle could be asked:

> Does a word *existent* exist in the search results?

In a vulnerable application, the above might be equivalent to:

> Does a query *?query=existent* return HTTP200 status code while *?query=non-existent* does not?"

The latter oracle could be formed from an [Error Event]({{< ref "./docs/attacks/error-events.md" >}}) XS-Leak and which could be abused by attackers to infer information about the user.

## Example

Websites are generally not allowed to access data on other websites. For example, *evil.com* is forbidden from explicitly reading a response from *bank.com*, but *evil.com* can attempt to load a script from *bank.com* since that was originally seen as harmless [^harmless]. However, *evil.com* can also determine whether or not the script successfully loaded.

{{< hint info >}}

Suppose that *bank.com* has an API endpoint that returns data about a user's receipt for a given query.

1. The page *evil.com* could attempt to load the URL *bank.com/my_receipt?q=groceries* as a script.
2. If the user has recently bought groceries, it will load successfully (because of HTTP200 status code).
3. But if they haven't, it will trigger an [Error Event]({{< ref "./docs/attacks/error-events.md" >}}) (because of HTTP404 status code) that *evil.com* can watch for.
4. By repeating this with different queries, the attacker could infer a significant amount of information about the user's transaction history
{{< /hint >}}

In the above example, two websites on two different origins (*evil.com* and *bank.com*) were interacting through an API that browsers allow websites to use. None of this exploited a bug in the browser or a bug in *bank.com*. But nonetheless, it allowed *evil.com* to leak some small amount of information about the user on *bank.com*.  



Browsers provide tons of different APIs that while well-intentioned, can end up leaking small amounts of information in an unintended way.

## Root Cause of XS-Leaks

The root cause of most XS-Leaks is inherent to the design of the web. Oftentimes, applications are vulnerable to some cross-site information leaks without having explicitly done anything wrong. Because it is hard [^hard-to-fix] to universally fix the root cause of XS-Leaks at the browser level, browsers are implementing various [Defense Mechanisms]({{< ref "defenses" >}}) that offer applications ways of mitigating some of the techniques. Many of these mitigations are used via websites opting into a more restrictive security model, usually through certain HTTP headers (e.g. *[Cross-Origin-Opener]({{< ref "./docs/defenses/opt-in/coop.md">}}): same-origin*), which often must be combined to achieve the desired outcome.

We can distinguish different sources of XS-Leaks, such as:

-   Browser APIs 

    -   For example: Frame Counting and Timing Attacks

-   Browser Implementation Details and Bugs

    -   For example: Connection Pooling and typeMustMatch

-   Hardware Bugs

    -   For example: Speculative Execution Attacks

## A little bit of history

XS-Leaks have always been part of the web platform but have only gained attention in recent years [^old-wiki] as websites became more secure against more traditional classes of vulnerabilities. In 2015 Gelernter and Herzberg published Cross-Site Search Attacks [^xs-search-first] which covered their work on exploiting timing attacks to implement XS-Search attacks against Google and Microsoft. From there, more XS-Leak techniques were discovered and tested over time. Recently, browsers have been working on developing a variety of new standards that will make it easier to defend applications against XS-Leaks.

## About the wiki


This wiki is meant to both introduce readers to XS-Leaks and serve as a reference guide to experienced researchers exploiting XS-Leaks. While this wiki contains information on many different techniques, new techniques are always emerging. Improvements, whether to add new techniques or improve existing pages, are always appreciated!

## References
[^side-channel]: Side Channel Vulnerabilities on the Web - Detection and Prevention, [link](https://owasp.org/www-pdf-archive/Side_Channel_Vulnerabilities.pdf)
[^csrf]: Cross Site Request Forgery (CSRF), [link](https://owasp.org/www-community/attacks/csrf)
[^STTF]: One of the examples for a feature with an accepted risk is [Scroll to Text Fragment]({{< ref "scroll-to-text-fragment.md" >}})
[^harmless]: Websites being able to interact and include resources from each other is a key part of how the web works. For example, many websites allow users to post content that includes images embedded from elsewhere on the web. Fundamentally, this is an intended behavior of the web. But, over time the downsides of this sort of interaction have become better understood.
[^hard-to-fix]: It is challenging to fix the root cause of XS-Leaks because in many cases doing so would break existing websites.
[^old-wiki]: Browser Side Channels, [link](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels)
[^xs-search-first]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)
