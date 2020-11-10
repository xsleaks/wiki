+++
title = "Framing Protections"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

A considerable number of XS-Leaks rely on some of the properties of iframes. If an attacker is unable to embed the contents of a page as an `iframe`, `frame`, `embed` or `object`, then the attack may no longer be possible. To mitigate XS-Leaks which rely on these objects, pages can forbid or select which origins can embed them. Doing so is possible by using the [`X-Frame-Options` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) or the [CSP frame-ancestors directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors).

Since a website enforcing Framing Protections can't be embedded from an attacker origin, the website is not rendered and the JavaScript does not run. Therefore, none of its subresources (images, JS, or CSS) are retrieved by the browser.

{{< hint tip >}}
The [CSP frame-ancestors directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors) is the more modern way of enabling framing protections. However, it is not supported by Internet Explorer, so in many cases it is recommended to use it in conjunction with the [`X-Frame-Options` header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options).
{{< /hint >}}

## Considerations

This protection is very effective against XS-Leaks that [rely on framing](../../../../abuse/iframes/) and can be easily implemented without breaking the vast majority of applications. This mechanism not only protects against some XS-Leaks, but also prevents attacks like [clickjacking](https://owasp.org/www-community/attacks/Clickjacking).

## Deployment

Deploying framing protections is usually straightforward as many applications are not meant to be embedded cross-origin in an `iframe`. Check out this [web.dev](https://web.dev/same-origin-policy/) article to learn more about the advantages of this header.
