+++
title = "Framing Protections"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

A considerable number of XS-Leaks relies on some properties of `frames`. If an attacker is unable to embed the contents of a page as an `iframe`, `frame`, `embed` or `object` then the attack may no longer be possible. To mitigate XS-Leaks which rely on these objects, pages can forbid or select which origins can embed them. This is possible by using the [`X-Frame-Options` Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) or the [CSP frame-ancestors directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors).

Since a website enforcing Framing Protections can't be embedded from an attacker origin, the website is not rendered and the JavaScript does not run. Therefore, all of its subresources (images, JS or CSS) are not retrieved by the browser.

{{< hint warning >}}
[`X-Frame-Options` Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) is obsolete and was replaced by the [CSP frame-ancestors directive](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors).
{{< /hint >}}

## Considerations

This protection is very effective against XS-Leaks which [rely on `iframes`](../../../../abuse/iframes/) and can be easily implemented without breaking the vast majority of applications. This mechanism not only protects from some XS-Leaks but also attacks like Clickjacking.

## Deployment

Deploying framing protections is usually straightforward as many applications do not require to be embedded cross-origin in an `iframe`. Check out this [web.dev](https://web.dev/same-origin-policy/) article to learn more about the advantages of this header.