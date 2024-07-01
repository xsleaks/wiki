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

## Retrieving user's history
Using the CSS [`:visited`](https://developer.mozilla.org/en-US/docs/Web/CSS/:visited) selector, it’s possible to apply a different style for URLs that have been visited.  
Previously it was possible to use [`getComputedStyle()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) to detect this difference, but now browsers prevent this by always returning values as if the link was visited and limiting what styles can be applied using the selector. [^changes-1]   
So, it may be needed to trick the user into clicking an area that the CSS has affected.
This can be done using [`mix-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode). [^blend-mode]   
There are also ways to do it without user interaction such as by abusing render timings.
This works because it takes time to paint links in a different color. [^render-timings]  
A Proof of Concept to this attack can be found in a Chromium report [^render-timings-bug] and it works by having multiple links to increase the time difference.

{{< hint info >}} 
This issue has been known for years and there are multiple open bugs to be fixed in multiple browsers [^bug-1] [^bug-2] [^bug-3]. There are also publicily available proof of concepts, e.g. Whack a mole game [^whack-a-mole] that requires user interaction for a successful leak.

[^whack-a-mole]: Whack a mole game, [link](https://lcamtuf.coredump.cx/whack/)
[^bug-1]: Issue 712246: Security: CSS :visited with mix-blend-mode can leak browser history, [link](https://crbug.com/712246)
[^bug-2]: Issue 713521: Eliminate :visited privacy issues once and for all, [link](https://crbug.com/713521)
[^bug-3]: :visited support allows queries into global history, [link](https://bugzilla.mozilla.org/show_bug.cgi?id=147777)
{{< /hint >}}

## Evil Captcha
Using CSS, it’s possible to take an embed out of context.  
An example of this is pretending it’s a captcha as seen in [^leak-2]  
This works by setting the width and height of an embed so that only the target characters are shown,
this may use multiple embeds to change the order of the characters being displayed so that its harder for a user to know what information they're providing.

## Abusing autocomplete
If a website uses text inputs and does not opt-out of [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete) using ```autocomplete="off"``` it may be possible to leak data such as email addresses by tricking the user into pressing the keys to navigate the autocomplete UI for a javascript focused text input.
For Chrome, this requires the user to be tricked into pressing the Up or Down arrow key which opens the dialog and selects a value, then by pressing Enter or Tab the value gets inserted into the page.
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
A custom cursor might not leak data directly but it may help trick the user, as a large cursor may overlay the autocomplete dialog and other native UI.
```html
<style>
:root {
  cursor: url('https://www.google.com/favicon.ico'), auto;
}
</style>
```

## Defense
[XFO]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) prevents embeds from being attacked because there's no visual difference as the content does not get shown.
The *Retrieving user's history* attack can only be prevented by the user.
This can be done by disabling the browser history, or if on Firefox, by setting the option `layout.css.visited_links_enabled` to `false` in `about:config` panel.  
| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ✔️                                 | ❌  |
## References 
[^changes-1]: Privacy and the :visited selector, [link](https://developer.mozilla.org/en-US/docs/Web/CSS/Privacy_and_the_:visited_selector)  
[^blend-mode]: CSS mix-blend-mode is bad for your browsing history, [link](https://lcamtuf.blogspot.com/2016/08/css-mix-blend-mode-is-bad-for-keeping.html)  
[^render-timings]: Pixel Perfect Timing Attacks with HTML5, [link](https://owasp.org/www-pdf-archive/HackPra_Allstars-Browser_Timing_Attacks_-_Paul_Stone.pdf)  
[^render-timings-bug]: Visited links can be detected via redraw timing, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=252165)
[^leak-2]: The Human Side Channel, [link](https://ronmasas.com/posts/the-human-side-channel)  
