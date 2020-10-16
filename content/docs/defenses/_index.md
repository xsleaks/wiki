---
weight: 20
bookFlatSection: true
title: "Defense Mechanisms"
---

# Defense Mechanisms

Defending against all [XS-Leaks Attack Vectors]({{< ref "../attacks/" >}}) is not a trivial task. Each one of them affects different web and browser components and has its quirks. Some bug bounty programs, such as Google VRP, even stopped paying for new XS-Leaks reports as they focus on large systemic changes to defend against XS-Leaks [^1]. Google and many other companies believe the right approach to fixing XS-Leaks is to invest time and engineering power into [new large scale mitigations and changes to the web platform]({{< relref "_index.md#opt-in-mechanisms" >}}) that applications can use to mitigate entire categories of XS-Leaks.

## Application Design

Application design [techniques]({{< ref "design-protections/_index.md" >}}) are about carefully designing the application in a way that it is resistant to XS-Leaks. This careful design helps develop temporary measures when [opt in mechanisms]({{< relref "_index.md#opt-in-mechanisms" >}}) can't be enforced on the application right away.

{{< hint warning >}}
Application design [techniques]({{< ref "design-protections/_index.md" >}}) should be seen as **temporary solutions**, before [opt-in mechanisms]({{< relref "_index.md#opt-in-mechanisms" >}}) are deployed. They are useful in particular situations but should not be the default strategy.
{{< /hint >}}

## Browser Default

Browsers vendors are actively working on [new mechanisms]({{< relref "_index.md#browser-default" >}}) that help mitigate some of the XS-Leaks mentioned in this wiki. 

{{< hint good >}}
Browser Default mechanisms can be highly effective. The biggest advantage is that it protects applications and users without additional effort from developers.
{{< /hint >}}

## Opt-in Mechanisms

These [defense mechanisms]({{< ref "opt-in/_index.md" >}}) allow applications to address classes of similar XS-Leaks at the same time. These protections can either allow applications to change the behavior of the browser or provide additional information that applications can use to change their own behavior. 

{{< hint good >}}
Deploying a combination of [opt-in defense mechanisms]({{< ref "opt-in/_index.md" >}}) should be the **default strategy**. Not only do they protect against XS-Leaks but also against other vulnerabilities such as XSSI, Clickjacking, CSRF, etc.
{{< /hint >}}

## References

[^1]: Google Bughunter University - XSLeaks and XS-Search, [link](https://sites.google.com/site/bughunteruniversity/nonvuln/xsleaks)
