+++
title = "Download Bar"
description = ""
date = "2024-04-23"
category = "Historical"
abuse = [
    "Downloads"
]
defenses = [
    "Deprecation"
]
menu = "main"
+++

In Chromium-based browsers, when a file was downloaded, a preview of the download process appeared in a bar at the bottom, integrated into the browser window. By monitoring the window height, attackers could detect whether the "download bar" opened:


```javascript
// Read the current height of the window
var screenHeight = window.innerHeight;
// Load the page that may or may not trigger the download
window.open('https://example.org');
// Wait for the tab to load
setTimeout(() => {
    // If the download bar appears, the height of all tabs will be smaller
    if (window.innerHeight < screenHeight) {
      console.log('Download bar detected');
    } else {
      console.log('Download bar not detected');
    }
}, 2000);
```

{{< hint important >}}
This attack was only possible in Chromium-based browsers with automatic downloads enabled. In addition, the attack can't be repeated since the user needs to close the download bar for it to be measurable again.
{{< /hint >}}

### Fix
Chromium moved away from a download bar, They explain why in the blog post [Redesigning Chrome downloads](https://blog.chromium.org/2023/08/redesigning-chrome-downloads-to-keep.html) it helps create a clearer separation of trusted browser UI from web content among other reasons.
