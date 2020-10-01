---
weight: 20
bookFlatSection: true
title: "Defense Mechanisms"
---

# Defense Mechanisms


Defending against all [XS-Leaks Attack Vectors]({{< ref "../attacks/" >}}) is not a trivial task. Each one of them affects different web and browser components and has its quirks. Some Bug Bounty programs, such as Google VRP even stopped receiving and paying for new XS-Leaks reports since they believe it's not feasible to prepare a [particular and unique bugfix]({{< relref "_index.md#application-design" >}}) for each report [^1]. Instead, Google and other companies believe the right approach towards fixing XS-Leaks is to invest time and human power into proposing [new large scale mitigations and changes to the web platform]({{< relref "_index.md#opt-in-mechanisms" >}}), so that applications can use them to fix considerable chunks of XS-Leaks.

## Application Design

Application Design [techniques]({{< ref "design-protections/_index.md" >}}) allow developers to better use certain `HTML APIs` known to easily introduce XS-Leaks. This type of techniques helps develop temporary measures when [opt in mechanisms]({{< relref "_index.md#opt-in-mechanisms" >}}) can't be enforced on the application right away.

{{< hint warning >}}
Application Design [techniques]({{< ref "design-protections/_index.md" >}}) should be seen as **temporary solutions**, before  [opt in mechanisms]({{< relref "_index.md#opt-in-mechanisms" >}}) are deployed. They are useful in particular situations, and should not be the default strategy.
{{< /hint >}}

## Browser Default

Browsers vendors actively discuss how to implement [new mechanisms]({{< relref "_index.md#browser-default" >}}) to help mitigate some of the XS-Leaks mentioned in this Wiki. They always try to make such mechanisms as transparent as possible to users and developers but this is not always possible.

{{< hint good >}}
Browser Default mechanisms can be highly effective. The biggest advantage is that it protects applications and users, and does not require extra efforts from developers.
{{< /hint >}}

## Opt-in Mechanisms

These [Defense Mechanisms]({{< ref "opt-in/_index.md" >}}) allow applications to address classes of similar XS-Leaks at once. By deploying these protections an application can instruct browsers to behave in a more secure way or it can leverage some additional information to block potential attacks.

{{< hint good >}}
Deploying a combination of [Opt-in Defense Mechanisms]({{< ref "opt-in/_index.md" >}}) should be the **default strategy**. Not only they protect against XS-Leaks but also against other vulnerabilities such as XSSI, Clickjacking, CSRF, etc.
{{< /hint >}}


[^1]: Google Bughunter University - XSLeaks and XS-Search, [link](https://sites.google.com/site/bughunteruniversity/nonvuln/xsleaks)
