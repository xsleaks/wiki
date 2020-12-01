---
weight: 1000
bookFlatSection: true
title: "Contributions"
---

# Contributions

This page explains how you can contribute to the XS-Leaks wiki and acknowledges the users who have contributed content.

## Contribution guidelines

The article source files reside in the `/content` directory in the [wiki repository](https://github.com/xsleaks/wiki/tree/master/content).

You can make changes to articles in various ways:

### Pull requests

In order to submit a pull request:
1. Fork the repository.
2. Make changes there and place them into a pull request.
3. Submit the pull request of the branch to master in the main folder.

If you are not sure about the folder structure, you can look up how other articles were written.

### Direct edits
Under every article, there is an `Edit this article` anchor which redirects you straight to the GitHub editor.

### Github issues
If neither of the above options work for you, we'd appreciate if you created a [new issue](https://github.com/xsleaks/wiki/issues/new) in the main wiki repository where you can explain the improvement, issue, or any other comment you have regarding the current version of the wiki.

## Local deployment
The wiki is built using the [Hugo framework](https://gohugo.io/getting-started/installing/).

You can run a local environment by following these steps:

1. Install the [Hugo Framework](https://gohugo.io/getting-started/installing/), **extended** version > 0.68.
2. Clone this repo.
3. Run `hugo server --minify` in the root directory.
4. Open your browser and go to http://localhost:1313 (or as indicated by the Hugo output).

## Wiki theme

We use the [Hugo Book Theme](https://themes.gohugo.io/hugo-book/) with custom modifications.

### Custom hint shortcode
We modified the default [Hints](https://themes.gohugo.io/theme/hugo-book/docs/shortcodes/hints/) used by the theme; the modified boxes are listed below:

{{< hint info >}}
```
{{</* hint info */>}} TEXT {{</* /hint */>}}
```
{{< /hint >}}

{{< hint note >}}
```
{{</* hint note */>}} TEXT {{</* /hint */>}}
```
{{< /hint >}}

{{< hint example >}}
```
{{</* hint example */>}} TEXT {{</* /hint */>}}
```
{{< /hint >}}

{{< hint tip >}}
```
{{</* hint tip */>}} TEXT {{</* /hint */>}}
```
{{< /hint >}}

{{< hint important >}}
```
{{</* hint important */>}} TEXT {{</* /hint */>}}
```
{{< /hint >}}

{{< hint warning >}}
```
{{</* hint warning */>}} TEXT {{</* /hint */>}}
```
{{< /hint >}}

### Original style
The original hint style can be used by adding a third parameter, `noTitle`, to the shortcode, e.g.:

{{< hint example noTitle>}}

`{{</* hint example noTitle */>}}`

{{< /hint >}}

## Acknowledgement

We would like to acknowledge the following users who [contributed](https://github.com/xsleaks/wiki/graphs/contributors) to this XS-Leaks wiki:

[Manuel Sousa](https://github.com/manuelvsousa), [terjanq](https://github.com/terjanq),
[Roberto Clapis](https://github.com/empijei), [David Dworken](https://github.com/ddworken),
[NDevTK](https://github.com/NDevTK)

In addition, we would also like to acknowledge the users who [contributed](https://github.com/xsleaks/xsleaks/wiki/Browser-Side-Channels/_history) to the predecessor of the current XS-Leaks wiki:

[Eduardo' Vela" \<Nava> (sirdarckcat)](https://github.com/sirdarckcat), [Ron Masas](https://github.com/masasron),
[Luan Herrera](https://github.com/lbherrera), [Sigurd](https://github.com/DonSheddow),
[larson reever](https://github.com/larsonreever), [Frederik Braun](https://github.com/mozfreddyb)
[Masato Kinugawa](https://github.com/masatokinugawa), [sroettger](https://github.com/sroettger)

And finally, our thanks go to all other amazing researchers that participate in sharing and exploring the depths of XS-Leaks!
