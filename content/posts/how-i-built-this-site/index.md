---
title: "How I Built This Site: A Technical Walkthrough"
subtitle: Building a Modern Multilingual Blog with Hugo, FixIt, and GitHub Pages
description: A step-by-step guide on how I built my Just DBA blog using Hugo, the FixIt theme, GitHub Pages, and various customizations.
date: 2025-05-02T00:52:04-04:00
keywords:
  - Hugo
  - FixIt
  - GitHub Pages
  - Blog
  - Multilingual
  - Database
  - Setup
tags:
  - Hugo
  - FixIt
categories: General
featuredImage: featured-image.webp
featuredImagePreview: ""
draft: false
---

# Introduction

This post documents the process of building my personal blog, **Just DBA**, which focuses on database management for PostgreSQL and Oracle. The site is built with [Hugo](https://gohugo.io/), uses the [FixIt theme](https://github.com/hugo-fixit/FixIt), and is hosted on GitHub Pages. Below, I share the steps, tools, and customizations involved..

<!--more-->

# 1. Project Initialization

I started by using a quick-start template based on Git submodules, provided by the FixIt theme maintainers:

- [hugo-fixit-starter1](https://github.com/hugo-fixit/hugo-fixit-starter1)

To set up the repository:

```sh
git clone --recursive https://github.com/msavdert/msavdert.github.io
cd msavdert.github.io
touch .nojekyll
code .
```

The `.nojekyll` file ensures GitHub Pages does not process the site with Jekyll, which is important for Hugo sites.

# 2. GitHub Pages Configuration

On GitHub, I configured the repository (`msavdert.github.io`) to use **GitHub Actions** for deployment:

- Go to **Settings > Pages > Source** and select **GitHub Actions**.

# 3. Basic Hugo Configuration

In `config/_default/hugo.toml`:

```toml
title = "Just DBA"
baseURL = "https://msavdert.github.io/"
hasCJKLanguage = false
```

# 4. Theme and Site Customization

I followed the [FixIt documentation](https://fixit.lruihao.cn/documentation/getting-started/configuration/#theme-configuration) to configure the theme and site parameters. Key customizations in `config/_default/params.toml` include:

- Site description, keywords, and author info
- Social links (GitHub, LinkedIn, Twitter, Instagram, RSS)
- Custom header with FontAwesome database icon
- Footer and home profile settings
- Enabling site views with Busuanzi
- Table of contents, sharing options, and more

Example:

```toml
description = "Just DBA - Database Management Expertise for PostgreSQL and Oracle"
keywords = ["Database", "PostgreSQL", "Oracle", "DBA", "SQL"]

[author]
  name = "Melih Savdert"
  email = "msg911x@gmail.com"
  link = "https://github.com/msavdert"

[header]
  [header.title]
    logo = ""
    name = "Just DBA"
    pre = "<i class='fas fa-database fa-fw' aria-hidden='true'></i>"
```

# 5. Favicon and Manifest

I generated favicons using [realfavicongenerator.net](https://realfavicongenerator.net/) and extracted the files under the `static` folder. I then created a custom partial at `layouts/partials/custom/head.html`:

```html
<link rel="icon" type="image/png" href="/favicon-96x96.png" sizes="96x96" />
<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
<link rel="shortcut icon" href="/favicon.ico" />
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png" />
<link rel="manifest" href="/site.webmanifest" />
```

And referenced it in `config/_default/params.toml`:

```toml
[app]
  noFavicon = true
[customPartials]
  head = [
    "inject/fixit-docs-bookmark.html",
    "custom/head.html",
  ]
```

# 6. Comments with Giscus

I enabled [Giscus](https://giscus.app/) for comments. Example configuration in `config/_default/params.toml`:

```toml
[page.comment]
  enable = true
  [page.comment.giscus]
    enable = true
    repo = "msavdert/msavdert.github.io"
    repoId = "R_kgDOOipdeA"
    category = "General"
    categoryId = "DIC_kwDOOipdeM4Cpp0N"
    mapping = "pathname"
    origin = "https://giscus.app"
    reactionsEnabled = "1"
    emitMetadata = "0"
    inputPosition = "bottom"
    lang = "en"
    lightTheme = "light"
    darkTheme = "dark"
    lazyLoad = true
```

# 7. Multilingual and Automatic Translation

To support multiple languages and automatic translation, I installed the [cmpt-translate](https://fixit.lruihao.cn/ecosystem/hugo-fixit/cmpt-translate/) component as a Git submodule:

```sh
cd msavdert.github.io
git submodule add https://github.com/hugo-fixit/cmpt-translate.git themes/cmpt-translate
```

I updated `config/_default/hugo.toml`:

```toml
theme = [
  "FixIt",
  "shortcode-docs-bookmark",
  "cmpt-translate"
]
defaultContentLanguage = "en"
disableLanguages = ["tr"]

[languages]
  [languages.en]
    weight = 1
    languageCode = "en"
    languageName = "English"
    hasCJKLanguage = false
  [languages.tr]
    weight = 2
    languageCode = "tr"
    languageName = "Türkçe"
    hasCJKLanguage = false
```

And set up translation partials and options in `config/_default/params.toml`:

```toml
[customPartials]
menuDesktop = [ "inject/translate-menu-desktop.html", ]
menuMobile = [ "inject/translate-menu-mobile.html", ]
assets = [ "inject/cmpt-translate.html", ]

[autoTranslate]
  enable = true
  service = 'client.edge'
  languages = ["turkish"]
  detectLocalLanguage = false
```

# 8. Content Organization

All blog posts are placed under `content/posts/` using the FixIt archetype structure. Each post can have its own folder with images and an `index.md` file for bundle support.

# 9. Commit and Deploy

After making changes, I commit and push to GitHub:

```sh
git add .
git commit -m "Update site content and configuration"
git push origin main
```

GitHub Actions automatically builds and deploys the site to GitHub Pages.

# Conclusion

This setup provides a modern, fast, and easily customizable blog with multilingual support, comments, analytics, and a professional look. For more details, check the [FixIt documentation](https://fixit.lruihao.cn/) and [my repository](https://github.com/msavdert/msavdert.github.io).
