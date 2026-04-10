# Melih Savdert Blog

This repository powers my public blog at [blog.savdert.com](https://blog.savdert.com).

It is the writing space for my shift from legacy DBA work toward platform engineering. I use it to publish technical notes, practical guides, and progress updates in public.

## What You Will Find Here

- platform engineering learning notes
- PostgreSQL and database reliability topics
- infrastructure and automation write-ups
- lessons from legacy systems and modernization
- short career transition posts written in plain English

## Repository Structure

```text
.
├── _posts/            # Published blog posts
├── _tabs/             # Static pages such as About, Archives, Tags
├── _data/             # Theme and contact data
├── assets/            # Images, icons, and static assets
├── docs/              # Writing guide and templates
└── .github/workflows/ # Build, deploy, and quality checks
```

## Local Development

Install dependencies:

```bash
bundle install
```

Run the site locally:

```bash
bundle exec jekyll serve -l
```

Build the site for production:

```bash
JEKYLL_ENV=production bundle exec jekyll build
```

## Writing Standard

Posts in this repository should be:

- short enough to stay readable
- technical enough to be useful
- written in clear and plain English
- honest about trade-offs, limits, and mistakes

See [docs/writing-guide.md](docs/writing-guide.md) for the editorial standard and [docs/post-template.md](docs/post-template.md) for a reusable post template.

## Publishing

The site is deployed through GitHub Pages using GitHub Actions. Content pushed to the main branch is built and published automatically.

## License

Unless noted otherwise, the written blog content in this repository is licensed under Creative Commons Attribution 4.0 International.
