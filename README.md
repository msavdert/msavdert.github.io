# Chirpy Starter

[![Gem Version](https://img.shields.io/gem/v/jekyll-theme-chirpy)][gem]&nbsp;
[![GitHub license](https://img.shields.io/github/license/cotes2020/chirpy-starter.svg?color=blue)][mit]

When installing the [**Chirpy**][chirpy] theme through [RubyGems.org][gem], Jekyll can only read files in the folders
`_data`, `_layouts`, `_includes`, `_sass` and `assets`, as well as a small part of options of the `_config.yml` file
from the theme's gem. If you have ever installed this theme gem, you can use the command
`bundle info --path jekyll-theme-chirpy` to locate these files.

The Jekyll team claims that this is to leave the ball in the user’s court, but this also results in users not being
able to enjoy the out-of-the-box experience when using feature-rich themes.

To fully use all the features of **Chirpy**, you need to copy the other critical files from the theme's gem to your
Jekyll site. The following is a list of targets:

```shell
.
├── _config.yml
├── _plugins
├── _tabs
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
├── tools/             # Local helper scripts
└── .github/workflows/ # Build, deploy, and quality checks
```

## Local Development

Install dependencies:

```bash
bundle install
```

Run the site locally:

```bash
./tools/run.sh
```

Build the site for production:

```bash
./tools/test.sh
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

This repository is published under the MIT License.
