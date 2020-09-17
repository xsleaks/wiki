+++
title = "XS-Search"
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

Cross-Site Search (XS-Search) is an important attack and principle in the family of XS-Leaks. The attack abuses Query-Based Search Systems to leak user information, from an attacker origin. The original attack used timing measurements to detect when a search system returned results or no results and works as follows:

1. Establish a baseline time for a request returning results (HIT), and a baseline for a request with no results (NO HIT).
2. Start a [timing attack](https://TODO) on the request to the search endpoint, brute-forcing the first character (`?q=a`).
3. If the measurement is under the HIT baseline then the attacker can move the next character, otherwise, try a new one.
4. In the end, a full secret can be leaked based on this timing measurements.

This attack requires multiple timing measurements to be accurate, something which can be improved with [Inflation Techniques](https://TODO) and statistical analysis. Also, instead of brute-forcing letter by letter, attackers can search specific words or sentences to leak only the occurrence of results.

The most important part of this attack is its [principle](https://TODO), as it can be applied in a different number of XS-Leaks depending on what a page does. For example


### Inflation Techniques

The inflation techniques of XS-Search are used to increase de accuracy of the attack with the purpose of making two request states easier to distinguish (results or no results). These two mechanisms will allow attackers to make better measurements:

- If a search system reflects certain GET parameters into the response when returning results, it will increase the size of the response. This will make the request more distinguishable because the time to prepare the response and send it over the network will grow substantially.
- Force the server to perform more computation work before returning a response. This can be possible in search systems offering some query language (e.g exclude terms in Gmail).


## Extended Principle

While considering the original Research around XS-Search an XS-Leak, the principle of the attack extends to other XS-Leaks.



## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:----------------------------------:|:--------------------------:|:---------------:|:-----:|:--------------------:|
| XS-Search (Timing)                 |         ✔️                 |      ✔️         |  ❌   |          ❌         |

## References

[^1]: Cross-Site Search Attacks, [link](https://446h.cybersec.fun/xssearch.pdf)
[^2]: Cross-Site Search (XS-Search) Attacks - Hemi Leibowitz, OWASP AppSec IL 2015, [link](https://owasp.org/www-pdf-archive/AppSecIL2015_Cross-Site-Search-Attacks_HemiLeibowitz.pdf)