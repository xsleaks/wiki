+++
title = "CSS Injection"
category = [
    "Attack"
]
abuse = [
    "CSS"
]
menu = "main"
weight = 2
+++

## CSS Injection

{{< hint warning >}}
This group of XS-Leaks requires a CSS injection on the target page.
{{< /hint >}}

Among the different CSS injection vectors, the most noticeable one is the abuse of CSS Selectors. They can be used as an expression to match and select certain HTML elements. For example, the selector `input[value^="a"]` is matched if the value of an `input` tag starts with the character "a". So, to detect if a CSS Selector matches the expression, attackers can trigger a callback to one of their websites using certain properties like `background`, `@import`, etc. [^1] [^2]. The matching process can easily be brute-forced, and extended to the full string.

Page content such as JavaScript can be leaked by abusing Font [ligatures](https://wikipedia.org/wiki/Ligature_(writing)) as a sequence of characters can have its own representation.

Some HTML tags that are normally hidden such as style and script can be rendered as text,
By applying a style like `* { display: block; }`.

Larger text dimensions can result in the scroll bar being shown,  
This scroll bar can have a custom style such as `background: url()` so that it makes a request to an attacker-controlled server when shown. [^3]

## Defense
- Put attacker controled content in its own document this can be done using a iframe with the srcdoc attrbute.
Optionaly include the sandbox attbute to isolate the content into its own origin.
- Use a CSS inliner so global styles get converted.

| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ❌                                 | ❌  |
## References
[^1]: CSS Injection Primitives, [link](https://x-c3ll.github.io/posts/CSS-Injection-Primitives/)
[^2]: HTTPLeaks, [link](https://github.com/cure53/HTTPLeaks/)
[^3]: Font ligatures, [link](https://sekurak.pl/wykradanie-danych-w-swietnym-stylu-czyli-jak-wykorzystac-css-y-do-atakow-na-webaplikacje/)
