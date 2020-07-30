---
weight: 1
bookFlatSection: true
title: "Defense Mechanisms"
---

# Defense Mechanisms


Defending against all [XS-Leaks Attack Vectors]({{< ref "../attacks/" >}}) is not a trivial task. Each one of them affects different web and browser components and has its quirks. Some Bug Bounty programs, such as Google VRP [^1] even stopped receiving and paying for new XS-Leaks reports since they believe it's not feasible to prepare a [particular and unique bugfix]({{< relref "_index.md#application-design" >}}) for each report. Instead, Google and other companies believe the right approach towards fixing XS-Leaks is to invest time and human power into proposing [new large scale mitigations]({{< relref "_index.md#opt-in-mechanisms" >}}), so that applications can use them to and fix considerable chunks of XS-Leaks.

Browsers also actively discuss how to incorporate [new mitigations]({{< relref "_index.md#browser-intrinsic" >}}) transparent to both users and developers, that help mitigate some of XS-Leaks mentioned in this Wiki.

## Application Design

Application Design techniques allow developers to better use certain `HTML APIs` known to easily introduce XS-Leaks. This type of techniques help develop temporary measures when [opt in mechanisms]({{< relref "_index.md#opt-in-mechanisms" >}}) can't be deployed in the application right away.

## Browser Intrinsic

TODO

## Opt-in Mechanisms

TODO


[^1]: Google Bughunter University - XSLeaks and XS-Search, [link](https://sites.google.com/site/bughunteruniversity/nonvuln/xsleaks)