---
weight: 1000
bookFlatSection: true
title: "Contributions"
---

# Contributions

## Guidelines

The contents of the articles lie in the `/content` directory in the [wiki repository](https://github.com/xsleaks/wiki/tree/master/content).

You can make changes to the articles in various ways:

### Pull requests

In order to submit a Pull Request:
1. Fork the repository.
2. Make changes there and put them into a pull request.
3. Submit a pull request of the branch to master in the main folder.

If you are not sure about the folder structure, you can look up how other articles were written.

### Direct edits
Under every article, there is `Edit this article` anchor which will redirect you directly to the GitHub editor.

### Github issues
If none of the above options work for you, it will be appreciated if you created a [new issue](https://github.com/xsleaks/wiki/issues/new) in the main wiki repository where you can explain the improvement, issue, or any other comment you have regarding the current version of the wiki.

## Local deployment
The wiki is built with the [Hugo framework](https://gohugo.io/getting-started/installing/).

You can run run the local environment via the following steps:

1. Install the [Hugo Framework](https://gohugo.io/getting-started/installing/) **extended** version > 0.68.
2. Clone this repo.
3. Run `hugo server --minify` in root directory.
4. Open your browser and go to http://localhost:1313 (or as indicated by hugo output).

## Wiki Theme

We use the [Hugo Book Theme](https://themes.gohugo.io/hugo-book/) with custom modifications.

### Custom hint shortcode.
We modified the default [Hints](https://themes.gohugo.io/theme/hugo-book/docs/shortcodes/hints/) from the theme and the modified boxes are listed below:

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
The original style can be used by adding a third parameter `noTitle` to the shortcode, e.g.

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

And all other amazing researchers that participate in sharing and exploring depths of XS-Leaks!
