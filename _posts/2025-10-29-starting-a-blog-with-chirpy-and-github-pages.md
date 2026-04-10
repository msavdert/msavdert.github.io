---
title: "Starting a Blog with Chirpy and GitHub Pages"
date: 2025-10-29 09:00:00 +0000
categories: [Blogging, Web Development]
tags: [jekyll, chirpy, github-pages, tutorial, blogging]
---

If you want to start a technical blog with a clean design, good defaults, and strong writing features, Chirpy is a very good choice.

This guide shows a simple way to start from zero and publish a blog with the Chirpy theme on GitHub Pages. It is written for people who want a practical setup, not a complicated one.

At the time of writing, this setup uses Chirpy 7.4.x.

## Why I Chose Chirpy

Chirpy is a Jekyll theme made for technical writing. It already includes many things that are useful for engineering blogs:

- a clean reading layout
- categories and tags
- syntax highlighting
- table of contents
- dark and light mode
- SEO and social metadata
- GitHub Pages friendly deployment

It lets you focus on writing instead of building a blog theme from scratch.

## What You Need

Before you start, make sure you have:

- a GitHub account
- Git installed
- Ruby and Bundler installed locally
- a repository where the blog will live

If you want your site at `https://yourname.github.io`, create a repository with that exact name.

## The Easiest Starting Point

The easiest way is to start from the official Chirpy starter project:

- [Chirpy Theme](https://github.com/cotes2020/jekyll-theme-chirpy)
- [Chirpy Starter](https://github.com/cotes2020/chirpy-starter)

This matters because the gem-based theme does not expose every file you need. In practice, a working Chirpy blog usually needs these core files in your repository:

- `_config.yml`
- `_plugins/`
- `_tabs/`
- `index.html`
- a GitHub Pages deployment workflow

So even if you install the theme gem, the starter repository is still the best reference for a complete setup.

## Basic Setup

Clone your blog repository and add Chirpy through Bundler.

Your `Gemfile` should contain this line:

```ruby
gem "jekyll-theme-chirpy", "~> 7.4"
```

Then install dependencies:

```bash
bundle install
```

If you start from the Chirpy starter, most of the structure is already ready.

## Required Repository Structure

The minimum practical structure looks like this:

```text
.
├── _config.yml
├── _plugins/
├── _posts/
├── _tabs/
├── assets/
├── .github/workflows/
└── index.html
```

You do not need a large custom codebase for a normal blog.

## Important Configuration

Most of your setup lives in `_config.yml`.

These fields matter first:

```yaml
lang: en
timezone: America/New_York

title: Your Blog Title
tagline: A short one-line description
description: >-
  A short summary of what your blog is about.

url: "https://yourdomain.com"
baseurl: ""

github:
  username: your-github-username

social:
  name: Your Name
  email: your@email.com
  links:
    - https://github.com/your-github-username
    - https://www.linkedin.com/in/your-linkedin

avatar: "/assets/img/avatar.png"
toc: true
```

### What These Settings Control

- `title`, `tagline`, and `description` control your site identity and SEO
- `url` must match your real site URL
- `baseurl` should stay empty for a root domain blog
- `social` links are used in the sidebar and metadata
- `avatar` is the profile image shown in the theme
- `toc` enables the table of contents in posts

## Pages You Will Usually Keep

Chirpy includes tab pages such as:

- About
- Archives
- Categories
- Tags

These live under `_tabs/`. You can edit them, keep them simple, or remove the ones you do not need.

## Writing Your First Post

Create a file inside `_posts/` using this format:

```text
YYYY-MM-DD-title-of-post.md
```

Example:

```text
2025-10-29-starting-a-blog-with-chirpy-and-github-pages.md
```

Each post needs front matter at the top:

```yaml
---
title: "My Post Title"
date: 2025-10-29 09:00:00 +0000
categories: [Blogging, Web Development]
tags: [jekyll, chirpy, github-pages]
---
```

Then write in normal Markdown.

One important rule with Chirpy: the post title already comes from front matter, so you usually do not need another `# H1` inside the post body.

## Running the Site Locally

To preview your blog locally:

```bash
bundle exec jekyll serve -l
```

Then open:

```text
http://127.0.0.1:4000
```

This is the fastest way to check formatting, links, and layout before pushing.

## GitHub Pages Deployment

The cleanest modern setup is to deploy with GitHub Actions.

Your workflow should do four things:

1. check out the repository
2. configure GitHub Pages
3. build the Jekyll site
4. upload and deploy the built site

This is better than relying on the old branch-based Pages flow because it is clearer and easier to maintain.

## Custom Domain

If you want to use your own domain:

1. add your domain in GitHub Pages settings
2. create the correct DNS record in your DNS provider
3. add a `CNAME` file to the repository root if needed
4. enable HTTPS after DNS is working

For subdomains, a `CNAME` record is usually the correct choice.

## Files You Probably Do Not Need

If your workflow is simple and you only write posts, you usually do not need extra template conveniences such as:

- `.devcontainer/`
- `.vscode/`
- local helper scripts you never use
- unused submodules

Keep the repository focused on content and deployment.

## How to Update Chirpy Later

This is the part that confuses many people.

If your `Gemfile` says:

```ruby
gem "jekyll-theme-chirpy", "~> 7.4"
```

that means:

- you allow patch updates inside `7.4.x`
- you do not automatically move to `7.5.x`

So your update process is:

1. check the Chirpy release notes
2. update the version constraint in `Gemfile` when you want the new minor release
3. run `bundle update jekyll-theme-chirpy`
4. review any release notes about breaking changes
5. compare your copied starter files with the latest starter project when needed

This last part is important. When Chirpy changes, the gem may update cleanly but your copied repository files may still need manual edits.

## My Recommendation

If you want a stable blog, keep your setup simple:

- use Chirpy through the gem
- keep `_config.yml` clean
- write posts under `_posts/`
- deploy through GitHub Actions
- update the theme only after reading release notes

That gives you a blog that feels modern without turning your blog repository into a large engineering project.

## Final Thought

Chirpy is a strong choice for engineers who want to publish technical writing with very little visual work.

You can start small, keep the repository clean, and grow the blog over time without rebuilding the whole platform.