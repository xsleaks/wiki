---
title: Introduction
type: docs
bookToc: false
---

# What are XS-Leaks?

XS-Leaks are a class of vulnerabilities that have been present on the web for a long time, gaining new attention at the end of 2018 when a formal name was assigned and a [repository](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels) of some of them was created. This class of vulnerabilities are considered Browser Side-channel Attacks and most of them exploit behaviors **inherent to the design of the web**, which increases the complexity around their mitigation.

## Why are XS-Leaks Hard to fix?

- The cause of most XS-Leaks is **inherent to the design of the web**. Applications can be vulnerable to some XS-Leaks without explicitly introducing them.
- Unlike common vulnerabilities such as `XSS`, with very specific taxonomies and attack consequences, XS-Leaks are an aggregation of several attack vectors with different implications.
- XS-Leaks are application behavior dependent, so their consequences can't be generalized.
- It's hard to fix XS-Leaks. Mitigating one XS-Leak can be irrelevant if all the others are possible to exploit. To be effective against most of them, applications must combine different types of [Defense Mechanisms]({{< ref "../docs/defenses/" >}}).

## The Principle of XS-Leaks

When `attacker.com` makes a request to `bank.com`, the browser will attach user cookies along with the request. The `Same-origin policy` is enforced and stop `attacker.com` from getting the contents of `bank.com`, and the principle of XS-Leaks is to go around this protection to **infer** Private Identifiable Information (PII) by watching the behavior of secondary events/channels (e.g Browser Cache, Request Timing).

Leaked information is **always** application dependent, varies depending on the features of the application and how it behaves in specific situations.

There are several types of XS-Leaks, and they have distinct origins:

- [`HTML` APIs]({{< ref "frame-counting.md" >}}) that allow attackers to access information about a page
- Browser Features which unintendedly introduced XS-Leaks
- Browser Bugs
- Inherent Web Platform Features (or a combination of them)

<!--TODO(manuelvsousa): Change this to TAG references-->

# About the Wiki

In this Wiki readers will be able to be introduced the topic and consult specifics of XS-Leaks, hopefully understanding their impact on web applications and what Defense Mechanisms can mitigate them.
This Wiki aggregates most of the XS-Leaks lost in the wild but frequently new ones are discovered. Feel free to help this Wiki grow and be up to date by contributing to the [XS-Leaks Github Repository](https://github.com/xsleaks/xsleaks.github.io-sources).
