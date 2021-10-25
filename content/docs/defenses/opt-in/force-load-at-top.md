+++
title = "force-load-at-top"
category = [
    "Defense",
]
menu = "main"
+++

Blocks hash navgations so that it always goes to the top of the document.

## Considerations

This protection is effective against XS-Leaks that require document navigation such as [scroll-to-text-fragment]{{< ref "/docs/attacks/experiments/scroll-to-text-fragment.md" >}}) abuse.

## Deployment

To deply for chromium just use the header `Document-Policy: force-load-at-top`
