---
title: "Building a Modern Tech Blog with Hugo, Blowfish, and GitHub Actions"
date: 2026-02-15
draft: false
description: "A detailed technical guide on how this blog was built, configured, and deployed using Hugo, the Blowfish theme, Git submodules, and a native Node.js 24 GitHub Actions pipeline."
tags: ["hugo", "blowfish", "github-actions", "ci-cd", "devops"]
categories: ["Infrastructure"]
---

Building a fast, lightweight, and modern developer blog doesn't have to require heavy server infrastructure or database systems. In this article, I will walk you through the exact technical details of how this blog was initialized, customized, and automated using Hugo, the Blowfish theme, Git submodules, and GitHub Actions.

---

## The Core Stack

- **Static Site Generator:** [Hugo](https://gohugo.io/) (known for being incredibly fast to build).
- **Theme:** [Blowfish](https://blowfish.page/) (a highly configurable, responsive, and aesthetically premium Hugo theme built with Tailwind CSS).
- **Hosting & CI/CD:** GitHub Pages deployed automatically via a GitHub Actions workflow.

---

## Step 1: Repository Initialization & GitHub Setup

To build a personal site hosted on GitHub Pages, we start by duplicating a starter template.

1. **Use the Template:** Go to the official [nunocoracao/blowfish_template](https://github.com/nunocoracao/blowfish_template) repository and click **"Use this template" -> "Create a new repository"**.
2. **Repository Naming:** Name your repository exactly **`<username>.github.io`** (for example, ours is `msavdert.github.io`). Using this exact format tells GitHub to serve this repository as your root personal domain. Make the repository **Public**.
3. **Configure Settings on GitHub:**
   - **GitHub Actions Workflow Permissions:** Navigate to `Settings -> Actions -> General`, scroll down to *Workflow permissions*, and select **"Read and write permissions"**. This is critical because the compilation process needs write access to deploy.
   - **GitHub Pages Deployment Source:** Navigate to `Settings -> Pages`. Under *Build and deployment -> Source*, change the dropdown selection from *Deploy from a branch* to **"GitHub Actions"**. This instructs GitHub to run your custom build workflow file (`.github/workflows/pages.yml`) rather than deploying raw files.

### Initializing the Theme Submodule Locally
When copying or cloning the template, the theme folder (`themes/blowfish`) is initialized as an empty directory reference. To download the actual theme source files, clone your newly created repository locally and run the following command in the root folder:

```bash
git submodule update --init --recursive
```

This populates `/themes/blowfish/` with all the files from the official [nunocoracao/blowfish](https://github.com/nunocoracao/blowfish) repository.

---

## Step 2: Configuration & Personalization

Hugo templates default to the creator's metadata. To customize it, three key TOML config files are modified:

### A. Core Site URL (`config/_default/hugo.toml`)
We uncomment and set the `baseURL` to the user's GitHub Pages domain:
```toml
baseURL = "https://msavdert.github.io/"
```

### B. Language & Author Info (`config/_default/languages.en.toml`)
Here we customize the title, meta descriptions, author headline, social links (GitHub, LinkedIn, Email), and biography:
```toml
[en]
  title = "Melih Savdert's Tech Blog"
  description = "Technical articles and guides on Platform Engineering, Kubernetes, DevOps, and Database Administration."
  
  [en.author]
    name = "Melih Savdert"
    headline = "Platform Engineer | DevOps | DBA | Kubernetes"
    bio = "Hi! I am Melih, a Platform Engineer specializing in Kubernetes, cloud infrastructure, database management, and automating software delivery."
    image = "img/avatar.png"
```

### C. Theme Visuals (`config/_default/params.toml`)
Blowfish supports a dark/light toggle and multiple pre-configured color schemes. For a clean developer aesthetic, the site is forced to `dark` mode and uses the premium `slate` color scheme. We also enable post listings on the homepage:
```toml
[theme]
  defaultAppearance = "dark"
  colorScheme = "slate"

[homepage]
  showRecent = true
  showRecentItems = 5
  showMoreLink = true
```

---

## Step 3: Modernizing the CI/CD Pipeline to Node.js 24

One of the common issues in standard workflows is Node.js version deprecation warnings (e.g., Node.js 20 deprecation). To ensure a warning-free and future-proof pipeline, the GitHub Actions workflow file (`.github/workflows/pages.yml`) was migrated to native Node.js 24 action versions:

- Upgraded checkout action to `actions/checkout@v6`
- Upgraded pages configuration to `actions/configure-pages@v6`
- Upgraded pages artifact upload to `actions/upload-pages-artifact@v5` (using `upload-artifact@v7` internally)
- Upgraded pages deployment to `actions/deploy-pages@v5`
- Upgraded Hugo build runner to `peaceiris/actions-hugo@v3`

This ensures that the build runner compiles our Hugo site using native Node.js 24 actions, completely avoiding deprecation warnings.

---

## Step 4: Maintenance & Theme Upgrades

Since the Blowfish theme is a Git submodule, upgrading it is straightforward.

### Checking the Current Version
To find the exact commit or version of Blowfish checking out:
```bash
git -C themes/blowfish describe --tags
```
*Output: `v2.103.0`*

### Upgrading to the Latest Theme Version
To pull down any upstream changes from the Blowfish repository and merge them into the tracking branch:
```bash
git submodule update --remote --merge
```

Alternatively, to pin to a specific release (like `v2.104.0`):
```bash
git -C themes/blowfish fetch --tags
git -C themes/blowfish checkout v2.104.0
```

Finally, pushing the updated submodule reference triggers the GitHub Actions rebuild:
```bash
git add themes/blowfish
git commit -m "Upgrade Blowfish theme to latest version"
git push origin main
```

---

## The Git-Backed Content Workflow

To add new content to this blog, I follow a set of simple, structured formatting rules:

### A. Folder Structure (Page Bundles)
Instead of putting a single flat `.md` file under `content/posts/`, I use **Hugo Page Bundles** (a folder per post). This encapsulates all resources (images, attachments) with the text itself.

*   **Structure:**
    ```text
    content/
    └── posts/
        └── my-new-post/
            ├── index.md           <-- The main markdown post
            ├── schema.png         <-- Image referenced by index.md
            └── performance.csv    <-- Supplemental attachment
    ```

### B. Front-Matter Metadata Setup
At the top of every `index.md` file, a YAML front-matter block contains the metadata that Blowfish reads to display card titles, summary descriptions, categories, and tags:

```yaml
---
title: "Your Post Title Here"
date: 2026-05-22
draft: false
description: "A short, 1-2 sentence description shown in SEO listings and search results."
tags: ["Kubernetes", "DevOps"] # Case-sensitive taxonomies
categories: ["Infrastructure"] # High-level groupings
# Optional settings:
# showAuthor: true
# featureImage: "schema.png"   # Relative path to directory image
# showTableOfContents: true
---
```

### C. Formatting and Images
*   **Headings:** Use `##` or `###` for headings. Avoid using `#` as the post title in front-matter is already mapped to the HTML `<h1>` header automatically.
*   **Images:** Keep images inside the post's bundle folder and reference them using clean relative paths:
    ```markdown
    ![Alt text describing image](schema.png)
    ```

### D. Drafting vs. Publishing
*   Set `draft: true` while writing. The local/CI build pipeline ignores drafts.
*   Set `draft: false` when you are ready to publish.

### E. Commit & Publish
Once written and verified, the post is pushed to GitHub:
```bash
git add content/posts/my-new-post/
git commit -m "feat: publish post about [topic]"
git push origin main
```
Within 60 seconds, GitHub Actions finishes the Hugo compilation and deploys the new post live to the global CDN at **`https://msavdert.github.io/`**.
