+++
title = "Cross-Origin-Opener-Policy"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

Getting access to a website's `window` object is a common prerequisite for different XS-Leak techniques. [Framing Protections]({{< ref "xfo.md" >}}) can ensure that an attacker cannot use iframes to access the `window` object, but this does not stop an attacker from accessing it from an opened window through `window.open(url)` or `window.opener` references. 

Exploiting XS-Leaks with `window.open` is generally seen as the least appealing option because the user can see it happen in the open browser window. However, it's usually the right technique when:

- A page sets [Framing Protections]({{< ref "xfo.md" >}})
- A page sets [Same-Site Cookies with `Lax` Mode]({{< ref "same-site-cookies.md" >}}) (since navigating a new window is a top-level navigation, it bypasses this)

To prevent other websites from gaining arbitrary window references to a page, applications can deploy Cross-Origin-Opener-Policy (COOP) [^1] [^2]. With COOP, applications are in control of who is allowed to have a reference to their pages, thus mitigating XS-Leaks which make use of such techniques.

## Considerations

Since COOP is an opt-in mechanism and a very recent one, it can easily be overlooked by developers and security engineers. Nonetheless, it’s important to highlight the importance of this defense mechanism as it is the only way to prevent attackers from exploiting XS-Leaks which makes use of window references returned by APIs like `window.open` (unless Same-Site Cookies Strict Mode can be widely deployed).

## Deployment

Check out this [web.dev](https://web.dev/why-coop-coep/) article to learn more about the advantages of this protection and how to deploy it.

## References

[^1]: Cross-Origin-Opener-Policy response header (also known as COOP), [link](https://gist.github.com/annevk/6f2dd8c79c77123f39797f6bdac43f3e)
[^2]: Cross-Origin-Opener-Policy, [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy)
