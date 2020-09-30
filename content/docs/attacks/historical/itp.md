+++
title = "WebKit - ITP"
description = ""
date = "2020-07-21"
category = "historical"
attacks = [
    "dom property",
]
defenses = [
    "TODO"
]
menu = "main"
+++


[Intelligent Tracking Prevention](https://webkit.org/tracking-prevention/) (ITP) is a privacy feature part of [WebKit Tracking Prevention technologies](https://webkit.org/tracking-prevention/). It’s a conjunction of several features to prevent a website from tracking a user under a third-party context. Unfortunately, the initial design introduced a new XS-Leak [^1], allowing attackers to abuse the states implicitly created by ITP to classify websites as trackers.

## Root Cause

To classify whether a website had tracking capabilities, ITP collects statistics on resource loads as well as user interactions in websites such as clicks, taps, or text entries. Based on the classification of these statistics, ITP gives a strike to a website if it is believed to have tracking capabilities. After 3 strikes a website is put on a deny list and have a different treatment by the browser in certain requests.

### Issues

One of the issues of ITP is that attackers can interact with it to arbitrarily enforce certain behaviors. For example, one could force ITP to give a strike to a domain and check if the domain entered the deny list. Some of the attacks leveraging ITP consist in:

- Checking if a website enters the deny list, or how many strikes left are needed to put a website on the deny list may allow an attacker to discover user's browsing habits.
- An attacker could attack a Search System with [XS-Search](https://TODO) if it includes any **cross-site resource** when results are present, but omits that same resource when no results are found. Attackers could force ITP to give 2 strikes to the **cross-site resource** and, after triggering a request to the search system, check if the resource was put in the deny list.

## Fix

To [fix the issue](https://webkit.org/blog/9661/preventing-tracking-prevention-tracking/), instead of relying on classifications, ITP now considers every site as a "tracking" one by default. This removes the implicit states which allowed attackers to detect certain ITP behaviors.

## References

[^1]: Information Leaks via Safari’s Intelligent Tracking Prevention, [link](https://arxiv.org/pdf/2001.07421.pdf)
[^2]: Preventing Tracking Prevention Tracking, [link](https://webkit.org/blog/9661/preventing-tracking-prevention-tracking/)
