---
title: "Starting a Blog with Chirpy and GitHub Pages"
date: 2025-10-29 09:00:00 +0000
categories: [Blogging, Web Development]
tags: [jekyll, chirpy, github-pages, tutorial, blogging]
---

If you want to start a technical blog with a clean design, good defaults, and strong writing features, Chirpy is an excellent choice. This guide shows the fastest path: use the Chirpy starter template, create your repository, and start writing posts immediately on GitHub Pages.

At the time of writing, this setup uses Chirpy 7.5.x.

## Why Chirpy

Chirpy is a Jekyll theme built specifically for technical writing. It includes everything you need out of the box:

- clean, responsive reading layout
- categories and tags for organization
- syntax highlighting for code
- automatic table of contents
- dark and light mode
- SEO and social metadata
- built-in search functionality
- PWA (Progressive Web App) support
- seamless GitHub Pages deployment

It lets you focus on writing instead of building a blog platform from scratch.

## Creating Your Blog Repository

The fastest way to start is with the official Chirpy starter template. You don't need to fork the full theme or set up anything locally first.

1. Go to [chirpy-starter](https://github.com/cotes2020/chirpy-starter) on GitHub
2. Click **Use this template** → **Create a new repository**
3. Name your repository `<username>.github.io` (replace `username` with your lowercase GitHub username)
4. Make the repository public (required for the GitHub Free plan)
5. Click **Create repository**

That's it. You now have a complete, working blog repository ready to deploy.

## Repository Structure

When you create from the starter, you already have everything you need:

```text
.
├── _config.yml              # Site configuration
├── _posts/                  # Your blog posts go here
├── _tabs/                   # Static pages (About, Archives, etc.)
├── _data/                   # Configuration data (contact, social)
├── assets/                  # Images, CSS, JavaScript
├── Gemfile                  # Ruby dependencies
├── index.html               # Homepage
├── CNAME                     # Custom domain (optional)
└── .github/workflows/       # GitHub Actions deployment
```

## Essential Configuration

Most setup happens in `_config.yml`. Update these key settings:

```yaml
lang: en
timezone: UTC                           # or your timezone

title: Your Blog Title
tagline: A short tagline
description: >-
  A brief description of your blog for search engines.

url: "https://yourusername.github.io"
baseurl: ""                             # Keep empty for root domain

github:
  username: your-github-username

avatar: "/assets/img/avatar.png"        # Profile picture
toc: true                               # Enable table of contents in posts
```

### Social Media Links

Edit `_data/contact.yml` to add your social profiles. These appear in the sidebar:

```yaml
- icon: fab fa-github
  text: GitHub
  link: https://github.com/your-username

- icon: fab fa-linkedin
  text: LinkedIn
  link: https://linkedin.com/in/your-profile
```

## Writing Your First Post

Posts go in the `_posts/` directory with this filename format:

```text
YYYY-MM-DD-post-title.md
```

Example: `2025-10-29-my-first-post.md`

Add front matter at the top of each post:

```yaml
---
title: "My First Post"
date: 2025-10-29 09:00:00 +0000
categories: [Blogging]
tags: [tutorial, writing]
---
```

Then write in regular Markdown. Chirpy gets your title from the front matter, so don't add an `# H1` header in the body.

## Deploying Your Blog

When you push to GitHub, the blog deploys automatically through GitHub Actions.

1. Clone your repository locally:
   ```bash
   git clone https://github.com/yourusername/yourusername.github.io.git
   cd yourusername.github.io
   ```

2. Create a new post file:
   ```bash
   # Create _posts/2025-10-29-hello-world.md
   # Add your post content
   ```

3. Commit and push:
   ```bash
   git add _posts/
   git commit -m "Add new post"
   git push
   ```

4. Visit `https://yourusername.github.io` (usually ready within a minute or two)

That's the entire workflow. Write posts locally, commit, push, and your blog updates automatically.

## Previewing Locally (Optional)

If you want to preview posts before publishing:

```bash
bundle install
bundle exec jekyll serve
```

Then open `http://127.0.0.1:4000` in your browser.

## Using a Custom Domain

If you want to use your own domain (like `blog.savdert.com`), this guide covers the complete setup: [Setting Up GitHub Pages with Custom Subdomain Using Cloudflare DNS]({{ site.baseurl }}/posts/github-pages-cloudflare-setup/)

The process is:
1. Set your custom domain in repository settings
2. Configure DNS records at your provider
3. GitHub automatically provisions HTTPS
4. Your blog is live at your custom domain

## Customizing the Blog

The starter includes default pages in `_tabs/`:
- **About** - your bio and introduction
- **Archives** - posts organized by date
- **Categories** - posts grouped by category
- **Tags** - posts grouped by tag

Edit these files directly or remove ones you don't need.

To customize CSS, copy `assets/css/jekyll-theme-chirpy.scss` from the theme and add your custom styles at the end.

## Updating Chirpy

The starter uses Chirpy as a gem:

```ruby
gem "jekyll-theme-chirpy", "~> 7.5"
```

This allows patch updates automatically. To update to a new minor version:

1. Update the version in `Gemfile`
2. Run `bundle update jekyll-theme-chirpy`
3. Check the [release notes](https://github.com/cotes2020/jekyll-theme-chirpy/releases) for changes

## Key Chirpy Features for Writing

**Front Matter Options:**

```yaml
---
title: "Post Title"
date: 2025-10-29 09:00:00 +0000
categories: [Category]
tags: [tag1, tag2]
pin: true                    # Pin post to top
---
```

**Built-in Markdown Support:**
- Code syntax highlighting with multiple languages
- Math expressions (LaTeX and MathML)
- Mermaid diagrams for flowcharts and charts
- Images with automatic lightbox
- Embedded YouTube videos

**Navigation and Organization:**
- Posts automatically organized by date
- Category and tag filtering
- Built-in search across all posts
- Automatic table of contents from headers

## My Recommendation

Keep your blog setup simple:

- Use the starter template (don't fork the full theme)
- Keep `_config.yml` minimal
- Write posts in `_posts/` following the naming convention
- Commit and push to deploy
- Update Chirpy only when you need new features

This approach keeps your repository focused on content, makes updates easier, and lets you grow your blog over time without unnecessary complexity.