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

Exploiting XS-Leaks with `window.open` is generally seen as the least appealing option for an attacker because the user can see it happen in the open browser window. However, it's usually the right technique when:

- A page sets [Framing Protections]({{< ref "xfo.md" >}})
- A page sets [Same-Site Cookies with `Lax` Mode]({{< ref "same-site-cookies.md" >}}) (in contrast to the `Strict` mode, navigating a top-level window is allowed by the `Lax` mode)

To prevent other websites from gaining arbitrary window references to a page, applications can deploy Cross-Origin-Opener-Policy (COOP) [^1] [^2]. 

There are three possible values for the COOP header. `unsafe-none` is the default value and is how websites behave if no value is set. `same-origin` is the strictest. If you set `same-origin`, then cross-origin websites cannot get access to your `window` object through opening new windows. If your application relies on using `window.open` to open another website and communicate with it, this will be blocked by `same-origin`. Instead, you can set `same-origin-allow-popups` which will allow your website to use `window.open` but does not allow other websites to use `window.open` against your application. 

If possible, it is recommended to set `same-origin`. If you set `same-origin-allow-popups` be sure to review what websites you open with `window.open` and ensure that they are trusted. 

## Considerations

Since COOP is an opt-in mechanism and a very recent one, it can easily be overlooked by developers and security engineers. Nonetheless, it’s important to highlight the importance of this defense mechanism as it is the only way to prevent attackers from exploiting XS-Leaks which makes use of window references returned by APIs like `window.open` (unless Same-Site Cookies in the Strict Mode can be widely deployed).

## Deployment

Check out this [web.dev](https://web.dev/why-coop-coep/) article to learn more about the advantages of this protection and how to deploy it.

## References

[^1]: Cross-Origin-Opener-Policy response header (also known as COOP), [link](https://gist.github.com/annevk/6f2dd8c79c77123f39797f6bdac43f3e)
[^2]: Cross-Origin-Opener-Policy, [link](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy)
