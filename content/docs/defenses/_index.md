---
weight: 20
bookFlatSection: true
title: "Defense Mechanisms"
---

# Defense Mechanisms

Defending against all possible [XS-Leaks Attack Vectors]({{< ref "../attacks/" >}}) is not a trivial task. Each one of the attack vectors affects different web and browser components and has its quirks. Some bug bounty programs, such as Google VRP, even stopped paying for new XS-Leaks reports as they are focusing on large systemic changes to defend against XS-Leaks [^1]. Google and many other companies believe that the right approach to fixing XS-Leaks is to invest time and engineering power into [new large scale mitigations and changes to the web platform]({{< relref "_index.md#opt-in-mechanisms" >}}) that applications can use to mitigate entire categories of XS-Leaks.

Browsers now provide a number of useful opt-in mechanisms that can be used to mitigate XS-Leaks. While these provide strong protections, the disadvantage is that they are not yet well supported by every browser. Defending against XS-Leaks effectively requires a mixture of different techniques, each of which is described in detail below.

## Application Design

Application design [techniques]({{< ref "design-protections/_index.md" >}}) are focused on carefully designing the application in a way that prevents XS-Leaks. This is a very useful approach when it is not practical to enable stronger global protections immediately. The other big advantage is that careful application design can stop XS-Leaks even on older browsers that don't support the newest browser standards.

{{< hint note >}}
It is very difficult to use application design techniques to block every XS-Leak technique across an entire application. While application design techniques are effective at stopping severe leaks, [opt-in mechanisms]({{< relref "_index.md#opt-in-mechanisms" >}}) provided by the browser are a better overall solution.
{{< /hint >}}

## Opt-in Mechanisms

These [defense mechanisms]({{< ref "opt-in/_index.md" >}}) allow applications to address classes of similar XS-Leaks at the same time. These protections can either allow applications to change the behavior of the browser or provide additional information that applications can use to change their own behavior.

{{< hint tip >}}
Deploying a combination of opt-in defense mechanisms should be the default strategy. Not only do they protect against XS-Leaks, but also against other vulnerabilities such as XSSI, Clickjacking, CSRF, etc.
{{< /hint >}}

{{< hint important >}}
When using any mitigations that rely on browser support, be sure to check that they are well supported by your customers' browsers. For example, `fetch metadata` headers are a great tool, but are currently only supported in Chromium-based browsers. Check [MDN](https://developer.mozilla.org/en-US/) for up-to-date information on browser support for different standards.
{{< /hint >}}

## Secure Defaults

Browser vendors are actively working on changing [default behaviors]({{< ref "secure-defaults/_index.md" >}}) to help mitigate some of the XS-Leaks mentioned in this wiki. Changing default behaviors is a balancing act between improving security and preserving backwards compatibility.

{{< hint important >}}
Secure defaults are amazing! They can help protect applications and users without any additional effort from developers. But note that they're unlikely to completely prevent XS-Leaks.
{{< /hint >}}

## References

[^1]: Google Bughunter University - XSLeaks and XS-Search, [link](https://sites.google.com/site/bughunteruniversity/nonvuln/xsleaks)
