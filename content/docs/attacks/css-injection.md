+++
title = "CSS Injection"
category = [
    "Attack"
]
abuse = [
    "CSS"
]
menu = "main"
weight = 11
+++

## CSS Injection

{{< hint warning >}}
This group of XS-Leaks requires a CSS injection on the target page.
{{< /hint >}}

Among the different CSS injection vectors, the most noticeable one is the abuse of CSS Selectors. They can be used as an expression to match and select certain HTML elements. For example, the selector `input[value^="a"]` is matched if the value of an `input` tag starts with the character "a". So, to detect if a CSS Selector matches the expression, attackers can trigger a callback to one of their websites using certain properties like `background`, `@import`, etc. [^1] [^2]. The matching process can easily be brute-forced, and extended to the full string.

Page content such as JavaScript can be leaked by abusing Font [ligatures](https://wikipedia.org/wiki/Ligature_(writing)) as a sequence of characters can have its own representation.

Some HTML tags that are normally hidden such as style and script can be rendered as text by applying a style like `* { display: block; }`. Hence, their content could be potentially leaked as well. 

Larger text dimensions can result in the scroll bar being shown,  
This scroll bar can have a custom style such as `background: url()` so that it makes a request to an attacker-controlled server when shown. [^3]

If loading external images for exfiltration is disallowed by a CSP img-src directive, you may still be able to use font URLs if they are not blocked by font-src, connect-src or default-src directives. You must first define a `@font-face { font-family: a; src: url(...) }`, and then reference it in a selector like `input[value^="a"] { font-family: a }`. This works because the font will only be loaded if it is required by some element on the page.

If no external requests can be made at all due to a strict CSP, it is still possible to use an XS-Leak based on browser crashing. At the time of writing, Chromium crashes when trying to render the `background: linear-gradient(in display-p3, red, blue)` property [^4]. Using CSS selectors this can be done conditionally.  
Because of [Full Site Isolation](https://chromium.googlesource.com/chromium/src/+/main/docs/process_model_and_site_isolation.md#Full-Site-Isolation-site_per_process), if one page of a site crashes, all other active frames of that site crash too. This is detectable by creating a dummy iframe on the attacker's page of the same site with any path, and measuring `onload=` events. By then conditionally crashing CSS in another iframe or popup window, you can detect the result of a single CSS selector by the dummy iframe crashing with it. Doing this repeatedly allows reading larger strings. [^5]

This last attack is often not fully mitigated by Framing Protections because any page on the same site with a `%00` error or a too long URI can serve as the dummy frame, which may be missing the regular response headers. The alternative becomes using popup windows, and only if the COOP protection is active will it be fully mitigated.

## Defense
- Put attacker controlled content in its own document this can be done using a iframe with the srcdoc attribute.
Optionally include the sandbox attribute to isolate the content into its own origin.
- Use a CSS inliner so global styles get converted.

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) | [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}}) |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :-------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ❌                                 |                                   ❌                                   |
## References
[^1]: CSS Injection Primitives, [link](https://x-c3ll.github.io/posts/2019/10/16/CSS-Injection-Primitives.html) ([alt](https://web.archive.org/web/20240619225434/https://x-c3ll.github.io/posts/CSS-Injection-Primitives/))
[^2]: HTTPLeaks, [link](https://github.com/cure53/HTTPLeaks/)
[^3]: Font ligatures, [link](https://research.securitum.com/stealing-data-in-great-style-how-to-use-css-to-attack-web-application/)
[^4]: Chrome tab crashes when using gradients in display-p3, rec2020, prophoto-rgb or a98-rgb, [link](https://issues.chromium.org/issues/382086298)
[^5]: x3CTF - blogdog, [link](https://jorianwoltjer.com/blog/p/ctf/x3ctf-blogdog-new-css-injection-xs-leak#xs-leak-using-process-crashing)
