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

Cross-Site Search (XS-Search) is an important attack and principle in the family of XS-Leaks. The attack abuses Query-Based Search Systems to leak user information from an attacker origin [^1] [^2]. The original attack used timing measurements to detect whether or not a search system returned results and works as follows:

1. Establish a baseline time for a request returning results (hit) and a baseline for a request with no results (miss).
2. Start a [timing attack]({{< ref "./timing-attacks/network-timing.md" >}}) on the request to the search endpoint, brute-forcing the first character (`?q=r`).
3. If the measurement is under the hit baseline then add one more character (`?q=ra`); otherwise try a new one (`?q=s`).
4. In the end, a full secret (`?q=secret`) can be leaked.

This attack requires multiple timing measurements to be accurate, something which can be improved with Inflation Techniques and statistical analysis. Furthermore, instead of brute-forcing letter by letter, attackers can search specific words or sentences to leak only the occurrence of results.

The most important part of this attack is its principle, as it can be applied in a different number of XS-Leaks.

## Inflation Techniques

The inflation techniques of XS-Search are used to increase the accuracy of the attack to make two responses easier to distinguish (hit or miss). These two mechanisms will allow attackers to make better measurements:

- If a search system reflects certain GET parameters into the response when returning results, it will increase the size of the response. This will make the request more distinguishable because the time to prepare the response and send it over the network will grow substantially.
- Force the server to perform more computation work before returning a response. This can be possible in search systems offering more expressive query languages (e.g exclude terms in Gmail will need to process every character in the results).

## Extended Principle

While the original research around XS-Search focused on timing attacks, the principle of the attack extends to other XS-Leaks. Instead of relying on timing measurements, which are unreliable, attackers can use other XS-Leaks to achieve the same observation.

In a Query-Based Search System, a user submits queries and gets responses associated with those queries. From this action, there are two different outcomes:

1. The system shows results and the page will present a specific behavior (first state).
2. The system does not show results and the page will present a different behavior from step 1 (second state).

If both behaviors, abstracted in the example above, can be distinguished using another XS-Leak technique, then an attacker can perform an XS-Search attack more reliably. For example, if the number of frames on a page varies based on search results (step 1 and 2 are distinguishable), this attack principle can be applied with a [Frame Counting]({{< ref "frame-counting.md" >}}) XS-Leak which is much more accurate then one using timing measurements.


## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| XS-Search (Timing)                 |         ✔️                 |      ✔️         |  ❌   |          ❌         |

## References

[^1]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)
[^2]: Cross-Site Search (XS-Search) Attacks - Hemi Leibowitz, OWASP AppSec IL 2015, [link](https://owasp.org/www-pdf-archive/AppSecIL2015_Cross-Site-Search-Attacks_HemiLeibowitz.pdf)