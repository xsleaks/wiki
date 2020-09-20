+++
title = "WebKit - ITP"
description = ""
date = "2020-07-21"
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++


[Intelligent Tracking Prevention](https://webkit.org/tracking-prevention/) (ITP) is a privacy feature part of WebKit Tracking Prevention technologies. It’s a conjunction of several features to prevent a website from tracking a user, under a third-party context. Unfortunately, the initial design introduced a new XS-Leak [^1], allowing attackers to abuse the states implicitly created by ITP to classify websites as trackers.

## Root Cause

To classify whether a website had tracking capabilities, ITP collected statistics on resource loads as well as user interactions in websites such as clicks, taps, or text entries.  Based on the classification of these statistics, ITP would give a strike to a website if it is believed to have tracking capabilities. After 3 strikes a website would be put on a deny list, and have a different treatment by the browser in certain requests.

### Problems

One of the issues of ITP was that attackers could interact with it to arbitrarily enforce certain behaviors. For example, one could force ITP to give a strike to a domain and check if the domain entered the deny list. Some of the attacks leveraging ITP consisted in:

- Checking if a website was put in the deny list, or how many strikes left are needed to put a website on the deny list could allow an attacker, leaking user’s browsing habits.
- An attacker could use [XS-Search](https://TODO) to attack a Search System if it includes any **cross-site resource** when results are present, but omits that same resource when no results were found. Attackers could create force ITP to give 2 strikes to the **cross-site resource**, and after triggering a request to the search system, check if the resource was put in the deny list.

## Fix

To [fix the issue](https://webkit.org/blog/9661/preventing-tracking-prevention-tracking/), instead of relying on classifications, ITP now considers every site as a "tracking" one by default. This removes the implicit states which allowed attackers to detect certain ITP behaviors.

<!--TODO(empijei): add a sentence or two?-->


## References

[^1]: Information Leaks via Safari’s Intelligent Tracking Prevention, [link](https://arxiv.org/pdf/2001.07421.pdf)
[^2]: Preventing Tracking Prevention Tracking, [link](https://webkit.org/blog/9661/preventing-tracking-prevention-tracking/)