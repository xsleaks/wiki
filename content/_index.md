---
title: Introduction
type: docs
bookToc: false
---

# XS-Leaks Wiki
## Overview

Cross-site leaks (aka XS-Leaks, XSLeaks) are a class of vulnerabilities derived from side-channels [^side-channel] built into the web platform. They take advantage of the web's core principle of composability, which allows websites to interact with each other, and abuse legitimate mechanisms to infer information about the user. One way of looking at XS-Leaks is to highlight their similarity with cross-site request forgery (CSRF [^csrf]) techniques, with the main difference being that instead of allowing other websites to perform actions on behalf of a user, XS-Leaks can be used to infer information about a user. 

Browsers provide a wide variety of features to support interactions between different web applications; for example, they permit a website to load subresources, navigate, or send messages to another application. While such behaviors are generally constrained by security mechanisms built into the web platform (e.g. the [same-origin policy](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)), XS-Leaks take advantage of small pieces of information which are exposed during interactions between websites. 

The principle of an XS-Leak is to use such side-channels available on the web to reveal sensitive information about users, such as their data in other web applications, details about their local environment, or internal networks they are connected to.

## Cross-site oracles

The pieces of information used for an XS-Leak usually have a binary form and are referred to as "oracles". Oracles generally answer with *YES* or *NO* to cleverly prepared questions in a way that is visible to an attacker. For example, an oracle can be asked:

> Does the word *secret* appear in the user's search results in another web application?

This question might be equivalent to asking:

> Does the query *?query=secret* return an *HTTP 200* status code?

Since it is possible to detect the *HTTP 200* status code with [Error Events]({{< ref "./docs/attacks/error-events.md" >}}), this has the same effect as asking:

> Does loading a resource from *?query=secret* in the application trigger the *onload* event?

The above query could be repeated by an attacker for many different keywords, and as a result the answers could be used to infer sensitive information about the user's data.

Browsers provide a wide range of different APIs that, while well-intended, can end up leaking small amounts of cross-origin information. They are described in detail throughout this wiki.

## Example

Websites are not allowed to directly access data on other websites, but they can load resources from them and observe the side effects. For example, *evil.com* is forbidden from explicitly reading a response from *bank.com*, but *evil.com* can attempt to load a script from *bank.com* and determine whether or not it successfully loaded.

{{< hint example >}}

Suppose that *bank.com* has an API endpoint that returns data about a user's receipt for a given type of transaction.

1. *evil.com* can attempt to load the URL *bank.com/my_receipt?q=groceries* as a script. By default, the browser attaches cookies when loading resources, so the request to *bank.com* will carry the user's credentials.
2. If the user has recently bought groceries, the script loads successfully with an *HTTP 200* status code. If the user hasn't bought groceries, the request fails to load with an *HTTP 404* status code, which triggers an [Error Event]({{< ref "./docs/attacks/error-events.md" >}}).
3. By listening to the error event and repeating this approach with different queries, the attacker can infer a significant amount of information about the user's transaction history.
{{< /hint >}}

In the example above, two websites of two different origins (*evil.com* and *bank.com*) interacted through an API that browsers allow websites to use.  This interaction didn't exploit any vulnerabilities in the browser or in *bank.com*, but it still allowed *evil.com* to gain information about the user's data on *bank.com*.  



## Root cause of XS-Leaks

The root cause of most XS-Leaks is inherent to the design of the web. Oftentimes applications are vulnerable to some cross-site information leaks without having done anything wrong. It is challenging to fix the root cause of XS-Leaks at the browser level because in many cases doing so would break existing websites. For this reason, browsers are now implementing various [Defense Mechanisms]({{< ref "defenses" >}}) to overcome these difficulties. Many of these defenses require websites to opt in to a more restrictive security model, usually through the use of certain HTTP headers (e.g. *[Cross-Origin-Opener-Policy]({{< ref "./docs/defenses/opt-in/coop.md">}}): same-origin*), which often must be combined to achieve the desired outcome.

We can distinguish different sources of XS-Leaks, such as:

1. Browser APIs (e.g. [Frame Counting]({{< ref "frame-counting.md" >}}) and [Timing Attacks]({{< ref "timing-attacks.md" >}}))
2. Browser implementation details and bugs (e.g. [Connection Pooling]({{< ref "./docs/attacks/timing-attacks/connection-pool.md" >}}) and [typeMustMatch]({{< ref "./docs/attacks/historical/content-type.md#typemustmatch" >}}))
3. Hardware bugs (e.g. Speculative Execution Attacks [^spectre])

## A little bit of history

XS-Leaks have long been part of the web platform;  [timing attacks]({{< ref "network-timing.md" >}}) to leak information about the user's web activity have been known since at least [2000](https://dl.acm.org/doi/10.1145/352600.352606).

This class of issues has steadily attracted more attention [^old-wiki] as new techniques were found to increase their impact. In 2015, Gelernter and Herzberg published "Cross-Site Search Attacks" [^xs-search-first] which covered their work on exploiting timing attacks to implement high impact XS-Search attacks against web applications built by Google and Microsoft. Since then, more XS-Leak techniques have been discovered and tested.

Recently, browsers have implemented a variety of new [defense mechanisms]({{< ref "defenses" >}}) that make it easier to protect applications from XS-Leaks.

## About this wiki

This wiki is meant to both introduce readers to XS-Leaks and serve as a reference guide for experienced researchers exploiting XS-Leaks. While this wiki contains information on many different techniques, new techniques are always emerging. Improvements, whether they add new techniques or expand existing pages, are always appreciated!

Find out how you can contribute to this wiki and view the list of contributors in the [Contributions]({{< ref "contributions.md" >}}) article.

## References
[^side-channel]: Side Channel Vulnerabilities on the Web - Detection and Prevention, [link](https://owasp.org/www-pdf-archive/Side_Channel_Vulnerabilities.pdf)
[^csrf]: Cross Site Request Forgery (CSRF), [link](https://owasp.org/www-community/attacks/csrf)
[^browser-features]: In some cases, these features are maintained to preserve backwards compatibility. But, in other cases, new features are added to browsers regardless of the fact that they introduce potential cross-site leaks (e.g. [Scroll to Text Fragment]({{< ref "scroll-to-text-fragment.md" >}})), as the benefits are considered to outweigh the downsides.
[^old-wiki]: Browser Side Channels, [link](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels)
[^xs-search-first]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)
[^spectre]: Meltdown and Spectre, [link](https://spectreattack.com/)
