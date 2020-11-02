+++
title = "Frame Counting"
description = ""
date = "2020-10-01"
category = [
    "Attack",
]
abuse = [
    "iframes",
]
defenses = [
    "Fetch Metadata",
    "SameSite Cookies",
    "COOP",
    "Framing Protections",
]
menu = "main"
weight = 2
+++


Window references allow cross-origin pages to get access to some attributes of other pages. These references become available when using or allowing `iframe` and `window.open`. They provide some information (although limited) about the window as they still respect the Same Origin Policy.

One of the accessible attributes is `window.length` which provides the number of frames in the window. This attribute can give valuable information about a page to an attacker.

Websites commonly use frames (or `iframes`) and this choice doesn't necessarily imply security issues.  There are however cases where a website might change the number of frames in a page depending on some user information. This could happen for example on a page that changes layout depending on the `GET` parameters and the victim data. It might be possible for an attacker to infer information about the victim by measuring the value of `window.length` on different pages. 

## Attack alternatives

In some cases, different application states have the same number of `frames`, preventing attackers from being able to distinguish them. However, continuously recording the frame count while the page is loading may show a pattern that might leak information to the attacker. 

```javascript
const win = window.open("https://target.page"); // Or any Window reference
const pattern = [];

const recorder = setInterval(() => pattern.push(win.frames.length), 0);

setTimeout(() => {
   clearInterval(recorder);
   console.log(pattern);
}, 6 * 1000);
```

## Case Scenarios

- A website lets a user search for user information in a search engine, if the page structure has a different number of `iframes` depending on whether there are results to the user query, an attacker could use the [XS-Search]({{< ref "xs-search.md" >}}) technique to leak those secrets.
- A website structures the user profile page differently based on gender or other PII. An attacker can easily leak this information by just opening the page and counting frames.

## Defense

| Attack Alternative  | [Same-Site Cookies]({{< ref "../defenses/opt-in/same-site-cookies.md" >}})  | [Fetch Metadata]({{< ref "../defenses/opt-in/fetch-metadata.md" >}})  | [COOP]({{< ref "../defenses/opt-in/coop.md" >}})  |  [Framing Protections]({{< ref "../defenses/opt-in/xfo.md" >}}) |
|:-------------------:|:------------------:|:---------------:|:-----:|:--------------------:|
| iframe              |         ✔️         |      ✔️         |  ❌   |          ✔️         |
| window.open         |         ✔️ [(if Strict)]({{< ref "../defenses/opt-in/same-site-cookies.md#lax-vs-strict" >}})         |      ✔️         |  ✔️   |          ❌         |


## Real World Example

A Vulnerability reported to Facebook used this technique to leak user-related information such as specific contents published in posts, religious information about friends, or photo locations[^1].

## References

[^1]: Patched Facebook Vulnerability Could Have Exposed Private Information About You and Your Friends. [link](https://www.imperva.com/blog/facebook-privacy-bug/)