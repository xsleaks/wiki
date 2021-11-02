+++
title = "Document policies"
category = [
    "Defense",
]
menu = "main"
+++

# Force load at top
Blocks hash navgations so that it always goes to the top of the document. 
This protection is effective against XS-Leaks that require document navigation such as [scroll-to-text-fragment]{{< ref "/docs/attacks/experiments/scroll-to-text-fragment.md" >}}) abuse.

## Deployment

To deploy for chromium just use the header `Document-Policy: force-load-at-top`. [^1]

There is also a chromium policy that can be used to disable just ScrollToTextFragment,  
For windows using the registry at `HKEY_CURRENT_USER\SOFTWARE\Policies\Google\Chrome` create a `DWORD` with the name `ScrollToTextFragmentEnabled` set to 0.
More infomation can be found [here](https://chromeenterprise.google/policies/#ScrollToTextFragmentEnabled)

# References
[^1]: Document-Policy: force-load-at-top, [link](https://www.chromestatus.com/feature/5744681033924608)
