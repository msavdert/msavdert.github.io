---
title: How to Publish Obsidian Notes as a Hugo Blog with GitHub Actions
subtitle: A Step-by-Step Guide for Beginners
description: Learn how to write blog posts in Obsidian, sync them with GitHub, and publish automatically to a Hugo static website using the FixIt theme and GitHub Actions.
aliases:
  - /how-to-publish-obsidian-notes-as-a-hugo-blog-with-github-actions/
date: 2025-05-02T11:40:39-04:00
keywords:
  - obsidian
  - hugo
tags:
  - obsidian
  - hugo
  - github
  - automation
categories: General
featuredImage: featured-image.png
featuredImagePreview: ""
draft: false
---

> **Reference:** This guide builds on the ideas in [How I Built This Site: A Technical Walkthrough][how-i-built-this-site] and uses the [hugo-fixit](https://github.com/hugo-fixit/FixIt) theme for publishing your static website.

If you want to write your blog posts in [Obsidian](https://obsidian.md/) using Markdown and publish them automatically to a Hugo static website hosted on GitHub Pages, this step-by-step guide is for you. Even if you have no prior experience, you’ll be able to set up a seamless workflow from note-taking to publishing.

---

## Overview

- **Write** your posts in Obsidian (Markdown).
- **Sync** your notes to a private GitHub repo using the [Obsidian Git plugin](https://github.com/denolehov/obsidian-git).
- **Automate** publishing: A GitHub Actions workflow copies your posts from your private repo to your public Hugo website repo.
- **Publish**: Your Hugo site (with the [FixIt theme](https://github.com/hugo-fixit/FixIt)) is updated automatically.

---

## Prerequisites

- A **private GitHub repo** for your Obsidian notes (e.g., `msavdert/obsidian-git-backup`).
- A **public GitHub repo** for your Hugo site (e.g., `msavdert/msavdert.github.io`), set up with the [hugo-fixit](https://github.com/hugo-fixit/FixIt) theme.
- [Obsidian Git plugin](https://github.com/denolehov/obsidian-git) installed in your Obsidian vault.

---

## Step 1: Create a Personal Access Token (PAT)

You need a GitHub Personal Access Token to allow your workflow to push changes to your public repo.

1. Go to [GitHub Settings > Developer settings > Personal access tokens](https://github.com/settings/tokens).
2. Click **"Generate new token"**.
3. Select the `repo` scope.
4. Copy the token (PAT) and **save it securely**.

---

## Step 2: Add the PAT as a Secret in Your Private Repo

1. Go to your **private repo** (`msavdert/obsidian-git-backup`) on GitHub.
2. Navigate to **Settings > Secrets and variables > Actions**.
3. Click **"New repository secret"**.
4. Name it `PUBLIC_REPO_PAT`.
5. Paste your PAT and save.

> **Note:** The secret must be added to the *private* repo, as the workflow will run there.

---

## Step 3: Create the GitHub Actions Workflow

1. In your **private repo**, create the directory: workflows (if it doesn’t exist).
2. Add a file named `msavdert/obsidian-git-backup/.github/workflows/sync_to_public.yml` with the following content:

````yaml
name: Sync Blog to Public Repo

on:
  push:
    branches:
      - main # Main branch of your private repo
    paths:
      - 'Blog/**' # Only trigger when Blog/ changes

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout private repo (source)
        uses: actions/checkout@v4

      - name: Checkout public repo (destination)
        uses: actions/checkout@v4
        with:
          repository: msavdert/msavdert.github.io
          token: ${{ secrets.PUBLIC_REPO_PAT }}
          path: public_repo
          fetch-depth: 0

      - name: Install rsync
        run: sudo apt-get update && sudo apt-get install -y rsync

      - name: Sync Blog directory to public repo's content/posts
        run: |
          echo "Syncing content from ./Blog/ to public_repo/content/posts/"
          mkdir -p public_repo/content/posts
          rsync -av ./Blog/ public_repo/content/posts/
          echo "Sync complete."

      - name: Commit and push changes to public repo
        run: |
          cd public_repo
          echo "Changes detected in public_repo/content/posts. Committing..."
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'
          git add content/posts
          git commit -m "chore: Sync blog posts from private repo"
          git push origin main
          echo "Changes pushed to public repo."
        env:
          GITHUB_TOKEN: ${{ secrets.PUBLIC_REPO_PAT }}
````

### How It Works

- **Trigger:** Runs when you push changes to `Blog/` in your private repo’s `main` branch.
- **Checkout:** Checks out both your private and public repos.
- **Sync:** Uses `rsync` to copy everything from `Blog/` to posts in your public Hugo repo.
- **Commit & Push:** Commits and pushes only if there are changes.

---

## Step 4: Write Your Blog Posts in Obsidian

Organize your posts in your Obsidian vault like this:

```
Blog/
  first-post/
    index.md
    image.webp
  second-post/
    index.md
```

### Example Obsidian Blog Post Template

````markdown
---
title: My First Post
subtitle: ""
description: ""
date: {{date:YYYY-MM-DD}}T{{time:HH:mm:ss}}{{time:Z}}
keywords: 
tags: 
categories: 
featuredImage: ""
featuredImagePreview: ""
draft: false
---
````

- Place each post in its own folder under `Blog/`.
- Images or attachments go in the same folder.

---

## Step 5: Push Your Notes and Publish

1. Use the **Obsidian Git plugin** to commit and push your changes to your private repo.
2. The GitHub Actions workflow will automatically copy your posts to your public Hugo repo.
3. Your Hugo site (with the [FixIt theme](https://github.com/hugo-fixit/FixIt)) will be updated and published via GitHub Pages.

---

## References

- [how-i-built-this-site/](https://github.com/msavdert/msavdert.github.io/blob/main/how-i-built-this-site/index.md)
- [Hugo FixIt Theme](https://github.com/hugo-fixit/FixIt)
- [Obsidian Git Plugin](https://github.com/denolehov/obsidian-git)

---

With this setup, you can write in Obsidian and publish to your Hugo blog with a simple git push!
