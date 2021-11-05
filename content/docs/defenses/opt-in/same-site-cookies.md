+++
title = "SameSite Cookies"
description = ""
date = "2020-10-01"
category = [
    "Defense",
]
menu = "main"
+++

SameSite cookies are one of the most impactful modern security mechanisms for fixing security issues that involve cross-site requests. This mechanism allows applications to force browsers to only include cookies in requests that are issued same-site [^1]. This type of cookie has three modes: `None`, `Lax`, and `Strict`.

## SameSite Cookie Modes

The following SameSite cookie modes are available:

* `None` – Disables all protections and restores the old behavior of cookies. This mode is not recommended.

  {{< hint important >}}
  The `None` attribute must be accompanied by the `Secure` flag [^same-site-none].
  [^same-site-none]: SameSite cookies explained, [link](https://web.dev/samesite-cookies-explained/#samesitenone-must-be-secure)
  {{< /hint >}}


* `Strict` – Causes the browser to not include cookies in any cross-site requests. This means `<script src="example.com/resource">`, `<img src="example.com/resource">`, `fetch()`, and `XHR` will all make requests without the SameSite `Strict` cookies attached. Even if the user clicks on a link to `example.com/resource`, their cookies are not included.

* `Lax` – The only difference between `Lax` and `Strict` is that `Lax` mode allows cookies to be added to requests triggered by cross-site top-level navigations. This makes `Lax` cookies much easier to deploy since they won't break incoming links to your application. Unfortunately, an attacker can trigger a top-level navigation via `window.open` that allows the attacker to maintain a reference to the `window` object.

## Considerations

`Strict` cookies provide the strongest security guarantees, but it can be very difficult to deploy `Strict` same-site cookies in an existing application.

SameSite cookies are neither bulletproof [^2] nor can they fix everything. To complement this defense strategy against XS-Leaks, applications should consider implementing other, additional protections. For example, [COOP]({{< ref "coop.md" >}}) can prevent an attacker from controlling pages using a `window` reference after the first navigation even if SameSite cookies in `Lax` mode are used.

{{< hint important >}}
Some browers may not use the default of Lax, So explicitly set the SameSite attrbute to ensure its enforced. 
By default, cookies in Chrome without `SameSite` attribute will default to `Lax` mode. However, there is an exception for that behavior for cookies set less than 2 minutes ago that are sent via POST requests. [^3]

[^3]: Cookies default to SameSite=Lax, [link](https://www.chromestatus.com/feature/5088147346030592)
{{< /hint >}}

## Deployment

Anyone interested in deploying this mechanism in web applications should take a careful look at this [web.dev](https://web.dev/samesite-cookie-recipes/) article.

## References

[^1]: SameSite cookies explained, [link](https://web.dev/samesite-cookies-explained/)
[^2]: Bypass SameSite Cookies Default to Lax and get CSRF, [link](https://medium.com/@renwa/bypass-samesite-cookies-default-to-lax-and-get-csrf-343ba09b9f2b)
