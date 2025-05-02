# Just DBA – Hugo Blog with FixIt Theme

This is a personal blog focused on database management (PostgreSQL, Oracle, and more), built with [Hugo](https://gohugo.io/) and the [FixIt](https://github.com/hugo-fixit/FixIt) theme. The site is deployed via GitHub Pages and supports multilingual content and automatic translation.

## Directory Structure

```
archetypes/    # Page archetypes (scaffolds)
assets/        # CSS, JS, images, etc.
config/        # Hugo configuration files
content/       # Blog posts and pages (Markdown)
static/        # Static files (favicon, manifest, etc.)
themes/        # Theme submodules (FixIt, cmpt-translate, etc.)
```

## Features

- Clean, responsive, and modern design
- Light/Dark mode support
- Multilingual and automatic translation (via cmpt-translate)
- SEO optimized and fast performance
- Social links and comment systems (Giscus, Disqus, etc.)
- Table of contents, code highlighting, and sharing options
- Easy deployment to GitHub Pages

## Quick Start

### Prerequisites

- [Hugo](https://gohugo.io/installation/) v0.132.0 or later (extended version)
- [Git](https://git-scm.com/)

### Clone the Repository

```bash
git clone --recursive https://github.com/msavdert/msavdert.github.io
cd msavdert.github.io
```

### Run Locally

```bash
hugo server
```

Visit `http://localhost:1313` in your browser.

### Build for Production

```bash
hugo
```

### Deploy to GitHub Pages

1. Configure GitHub Actions and Pages as described in the [FixIt documentation](https://fixit.lruihao.cn/documentation/getting-started/).
2. Set the `baseURL` in hugo.toml to your site URL.
3. Push changes to the `main` branch; deployment is automatic.

## Theme and Customization

- Main theme: [FixIt](https://github.com/hugo-fixit/FixIt)
- Automatic translation: [cmpt-translate](https://github.com/hugo-fixit/cmpt-translate)
- Customizations are managed in params.toml and assets

## Updating Themes

```bash
git submodule update --remote --merge themes/FixIt
```

## Community & Support

- [FixIt Discussions](https://github.com/hugo-fixit/FixIt/discussions)
- [FixIt Documentation](https://fixit.lruihao.cn/documentation/)

## License

This project is licensed under the MIT License.
