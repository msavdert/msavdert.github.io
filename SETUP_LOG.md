# GitHub Pages Blog Setup & Configuration Log

This document tracks all initialization steps, custom configurations, assets, content structure, and bug fixes applied to the `msavdert.github.io` repository setup using the Blowfish Hugo Theme template.

---

## 1. Repository Initialization & GitHub Setup
- **Template Source:** Created from [nunocoracao/blowfish_template](https://github.com/nunocoracao/blowfish_template) using the **"Use this template"** feature on GitHub.
- **Repository Name:** Named exactly **`msavdert.github.io`** under the username `msavdert` to serve as the user's root GitHub Pages site.
- **Access Level:** Configured as a **Public** repository to ensure seamless integration with GitHub Pages.

### GitHub Repository Settings Configured:
1. **GitHub Actions Permissions:**
   - Navigated to `Settings -> Actions -> General -> Workflow permissions`.
   - Changed default permissions to **"Read and write permissions"** to allow automatic builds to write the compiled site back to the pages branch/environment.
2. **GitHub Pages Deployment Source:**
   - Navigated to `Settings -> Pages -> Build and deployment -> Source`.
   - Changed from the default *Deploy from a branch* to **"GitHub Actions"** to allow the workflow file (`.github/workflows/pages.yml`) to orchestrate the entire deployment process automatically.

---

## 2. Local Repository Fixes & Git Submodule Initialization
When copying Hugo templates, the theme directory (`themes/blowfish`) is initialized as an empty directory reference.
- **Fix:** Ran the following command locally to download the actual source code of the Blowfish Hugo Theme:
  ```bash
  git submodule update --init --recursive
  ```
- **Result:** Successfully populated `/themes/blowfish/` with the official Blowfish theme components.

---

## 3. URL Customization (nunocoracao -> msavdert)
The initial template defaults pointed to the original repository's pages URL (`nunocoracao.github.io/blowfish_template`). These were replaced with the user's specific domain to avoid broken resource paths and incorrect deploy endpoints:

### Files Updated:
1. **`config/_default/hugo.toml`**:
   - Uncommented and updated `baseURL`:
     ```toml
     baseURL = "https://msavdert.github.io/"
     ```
2. **`.github/workflows/pages.yml`**:
   - Updated Hugo build command `baseURL` flag:
     ```yaml
     hugo -d ./public --baseURL https://msavdert.github.io/
     ```
   - Updated deployment environment target URL:
     ```yaml
     url: https://msavdert.github.io/
     ```

---

## 4. GitHub Actions Node.js 24 Native Migration
Originally, the workflow was generating warnings about Node.js 20 deprecation. To fix this cleanly and permanently without runner warning side-effects, all actions in the workflow were upgraded to versions that natively target Node.js 24:

### Upgrades Applied:
- **`actions/checkout@v4`** -> upgraded to **`actions/checkout@v6`** (natively targets Node.js 24)
- **`actions/configure-pages@v5`** -> upgraded to **`actions/configure-pages@v6`** (natively targets Node.js 24)
- **`actions/upload-pages-artifact@v3`** -> upgraded to **`actions/upload-pages-artifact@v5`** (natively targets Node.js 24, using `upload-artifact@v7` internally)
- **`actions/deploy-pages@v4`** -> upgraded to **`actions/deploy-pages@v5`** (natively targets Node.js 24)
- **`peaceiris/actions-hugo@v2`** -> upgraded to **`peaceiris/actions-hugo@v3`** (natively targets Node.js 24)

By moving all actions to their native Node.js 24 version tags, the `FORCE_JAVASCRIPT_ACTIONS_TO_NODE24` global override was removed completely, achieving a warning-free, future-proof, native build and deploy pipeline.

---

## 5. Website Customization & Personalization
To tailor the website as a modern technology blog focusing on **Platform Engineering, DevOps, DBA, and Kubernetes**, the following custom styles, configurations, and assets were applied:

### A. Theme parameters (`config/_default/params.toml`)
- **Default Appearance:** Forced to **`dark`** by default for a clean developer aesthetic.
- **Color Scheme:** Switched theme styling to the premium **`slate`** theme color scheme.
- **Homepage Settings:** 
  - Enabled listing of latest articles on the homepage: `homepage.showRecent = true`.
  - Configured displaying up to 5 articles on the landing page.
  - Enabled "Show More" link pointing to `/posts`.
- **Interactivity:** Ensured search `enableSearch = true` and code block copying `enableCodeCopy = true` are active.

### B. Language & Author Info (`config/_default/languages.en.toml`)
- **Site Title:** Set to `"Melih Savdert's Tech Blog"`.
- **Meta Description:** Set to `"Technical articles and guides on Platform Engineering, Kubernetes, DevOps, and Database Administration."`
- **Author Profile:**
  - Name: `Melih Savdert`
  - Headline: `Platform Engineer | DevOps | DBA | Kubernetes`
  - Biography: `Hi! I am Melih, a Platform Engineer specializing in Kubernetes, cloud infrastructure, database management, and automating software delivery.`
  - Avatar: Pointed to `img/avatar.png` (using the custom asset).
  - Social Links: Activated links for Email, GitHub, and LinkedIn.

### C. Menus (`config/_default/menus.en.toml`)
- Enabled header navigation links for:
  - **Blog** (pointing to `/posts/`)
  - **Categories** (pointing to `/categories/`)
  - **Tags** (pointing to `/tags/`)

### D. Custom Assets (`assets/img/avatar.png`)
- Generated and saved a premium 2D vector tech icon at `assets/img/avatar.png` featuring a stylized server rack combined with a database cylinder and a Kubernetes helm icon in cyan/purple neon gradient.

### E. Content Structure (`content/posts/hello-world/index.md`)
- Created a starter post introducing the blog's key topics.
- Added front-matter metadata tags: `"Platform Engineering"`, `"Kubernetes"`, `"DevOps"`, `"DBA"` to initialize Hugo's tag/category taxonomy views.

---

## 6. Summary of Commands for Deployment
Whenever changes are made to configurations, styles, or markdown contents, deployment is handled entirely via Git pushes:
```bash
git add .
git commit -m "<commit message>"
git push origin main
```
The GitHub Actions workflow then compiles the site using Hugo and deploys it live to **`https://msavdert.github.io/`**.
