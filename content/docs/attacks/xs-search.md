+++
title = "XS-Search"
description = ""
date = "2020-10-01"
category = [
    "Attack",
    "Attack Principle",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
]
menu = "main"
weight = 1
+++

Cross-site search (XS-Search) is an important attack principle in the family of XS-Leaks. This type of attack abuses Query-Based Search Systems to leak user information from an attacker origin [^1] [^2]. The original attack uses timing measurements to detect whether or not a search system returns results and works as follows:

1. Establish a baseline of the time needed for a request to return results (hit), and a baseline for the time needed by a request with no results (miss).
2. Start a [timing attack]({{< ref "./timing-attacks/network-timing.md" >}}) on the request to the search endpoint, brute-forcing the first character (`?q=r`).
3. If the measurement is under the hit baseline, then add one more character (`?q=ra`); otherwise try a new one (`?q=s`).
4. In the end, a full secret (`?q=secret`) can be leaked.

This attack requires multiple timing measurements to be accurate, something which can be improved with inflation techniques and statistical analysis. Furthermore, instead of brute-forcing letter by letter, attackers can search specific words or sentences to leak only the occurrence of results.

The most important part of this attack is its principle, as it can be applied to a number of different XS-Leaks.

## Inflation Techniques

The inflation techniques of XS-Search are used to increase the accuracy of the attack to make the two response types (hit or miss) easier to distinguish. The following two mechanisms  allow attackers to make better measurements:

- If a search system reflects certain GET parameters into the response when returning results, the size of the response increases. This makes the request more distinguishable because the time to prepare the response and send it over the network grows substantially.
- Force the server to perform more computation work before returning a response. This approach can be applied in search systems offering more expressive query languages (e.g. exclude terms in Gmail needs to process every character in the results).

## Extended Principle

While the original research around XS-Search focused on timing attacks, the principle of the attack extends to other XS-Leaks. Instead of relying on timing measurements, which are unreliable, attackers can use other XS-Leaks to extract the same information.

In a Query-Based Search System, a user submits queries and gets responses associated with those queries. This action can result in two different outcomes:

1. The system shows results and the page behaves in a specific way (first state).
2. The system does not show results and the page behaves in a different way than in step 1 (second state).

If both behaviors above can be distinguished by a more reliable XS-Leak than timing, then an attacker could perform a more efficient XS-Search attack. For example, if the number of frames on a page varies based on search results (step 1 and 2 are distinguishable), this attack principle can be applied with a [Frame Counting]({{< ref "frame-counting.md" >}}) XS-Leak which could be more accurate than one using timing measurements.


## Defense

| Attack Alternative | [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                                          [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                                          |
| :----------------: | :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------------------------------------------------: |
| XS-Search (timing) |                                         ✔️                                          |                          ❌                          |                                 ❌                                 | [RIP]({{< ref "/docs/defenses/isolation-policies/resource-isolation" >}}) 🔗 [NIP]({{< ref "/docs/defenses/isolation-policies/navigation-isolation" >}}) |

🔗 – Defense mechanisms must be combined to be effective against different scenarios.

## References

[^1]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)
[^2]: Cross-Site Search (XS-Search) Attacks - Hemi Leibowitz, OWASP AppSec IL 2015, [link](https://wiki.owasp.org/images/a/a7/AppSecIL2015_Cross-Site-Search-Attacks_HemiLeibowitz.pdf) ([alt](https://web.archive.org/web/20231226222426/https://owasp.org/www-pdf-archive/AppSecIL2015_Cross-Site-Search-Attacks_HemiLeibowitz.pdf))   
