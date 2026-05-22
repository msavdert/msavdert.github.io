# Blowfish Theme Makeover & Advanced Customization Guide

This technical guide outlines the step-by-step procedures to customize, style, and optimize the Blowfish Hugo theme. The configuration strategies, custom CSS overrides, and workflow automations detailed below are modeled after the design architecture of the theme creator's personal website (`n9o.xyz`).

---

## 1. Homepage Transition: Profile to Background Card Grid

By default, the Blowfish template initializes with a `profile` layout (an author profile card with social links). Transitioning to a `background` layout displays a card grid of recent articles overlaid on a blurred background image, providing a highly immersive visual experience.

### Step A: Configure `params.toml`
Open `config/_default/params.toml` and locate the `[homepage]` section. Modify the parameters as follows:

```toml
[homepage]
  layout = "background"          # Options: "page", "profile", "hero", "card", "background", "custom"
  homepageImage = "background.jpg" # Filename of the background image (must reside in assets/ root)
  showRecent = true              # Enables listing of recent posts on homepage
  showRecentItems = 12           # Max number of posts/cards to render on the homepage
  cardView = true                # Renders homepage articles as grid cards instead of simple lists
  cardViewScreenWidth = false    # Limits cards to container width (true spans full screen)
  layoutBackgroundBlur = true    # Applies a modern glassmorphic blur to the background image behind the content
  showMoreLink = true            # Adds a link to view more articles
  showMoreLinkDest = "/posts"    # Target location for the "Show More" link
```

### Step B: Add the Background Asset
1. Select a high-resolution, landscape-oriented image (e.g., `background.jpg`).
2. Save this image directly in the **root** of the `assets/` directory:
   ```text
   assets/
   └── background.jpg            <-- Save file here
   ```
> [!IMPORTANT]
> The background image must be placed directly at the root of `assets/`. Placing it in `static/` or `assets/img/` will cause Hugo's asset pipeline to miss it, resulting in a blank homepage background.

---

## 2. Navigation Header: Fixed/Sticky Navigation

To keep the menu navigation bar visible at all times when a user scrolls down, convert the header to a `fixed` layout and configure section highlighting.

### Step A: Update Header Layout in `params.toml`
Under the `[header]` section, modify the layout type:

```toml
[header]
  layout = "fixed"               # Options: "basic", "fixed", "sticky"
```

### Step B: Enable Current Menu Area Highlighting
Ensure that the active navigation item (e.g., "Blog" when browsing `/posts/`) highlights dynamically as the user navigates:

```toml
highlightCurrentMenuArea = true
```

---

## 3. Post Reading Experience: Smart Table of Contents (TOC)

For long-form technical posts, a sticky Table of Contents (TOC) on the right sidebar enhances navigation. The "smart" TOC dynamically highlights the heading corresponding to the section the user is currently reading.

### Step A: Enable TOC Globally in `params.toml`
Set the global TOC parameters under `config/_default/params.toml`:

```toml
smartTOC = true                         # Enables dynamic active-heading highlighting
smartTOCHideUnfocusedChildren = false   # Set to true to collapse inactive nested sub-headings
```

Also, verify that the article defaults show the TOC:
```toml
[article]
  showTableOfContents = true
```

### Step B: Toggle TOC on a Per-Post Basis
You can override global settings by adding `showTableOfContents` directly to an individual article's front-matter in its `index.md`:

```yaml
---
title: "Advanced Kubernetes Controllers"
date: 2026-05-22
showTableOfContents: false  # Disables TOC for this specific post
---
```

---

## 4. Custom CSS Override System

To customize component styling (such as adding shadows to cards or modifying logo sizes) without touching the core theme files, leverage the theme's built-in asset compilation process.

### Step A: Create the Custom Stylesheet
Create the directory and stylesheet in your repository as follows:
```text
assets/
└── css/
    └── custom.css               <-- Create this file
```

### Step B: Add Custom Styling Rules
Add the CSS classes Nuno Coração uses to style cards and entities:

```css
/* Apply modern deep shadows to article card thumbnails */
@media screen and (min-width: 0px) {
    .customthumb {
        width: 100%;
        height: 220px;
        object-fit: cover;
        box-shadow: 10px 10px 30px 2px rgba(0, 0, 0, 0.6); /* Smooth dark card shadow */
    }

    /* Styles for client/employer logos in experience lists */
    .customEntitityLogo {
        background-color: aliceblue;
        position: relative;
        min-width: 60px;
        width: 60px;
        height: 60px;
        margin: 10px 0 10px 8px;
        padding: 5px;
        object-fit: contain;
        border-radius: 4%;
    }
}
```

---

## 5. Analytics & Article View Counter

Displaying article read views directly on the post cards and article headers requires linking an analytics ID.

### Step A: Configure Google Analytics
Open `config/_default/hugo.toml` (or `config/_default/config.toml`) and define your Google Analytics tag:

```toml
googleAnalytics = "G-R94R4YKFEC" # Replace with your active GA4 Tag
```

### Step B: Enable View Counts in `params.toml`
Configure Blowfish to display the views next to reading times and publication dates:

```toml
[article]
  showViews = true               # Displays view count on individual article headers
  showViewsOnMinicards = true    # Displays view count on small article list cards
```

---

## 6. Static Theme Enforcement ( slate & dark mode )

If you want your technical blog to enforce a permanent slate-dark aesthetic and ignore system-level light-mode changes, configure the following in `params.toml`:

```toml
colorScheme = "slate"            # Set the primary color theme to slate
defaultAppearance = "dark"       # Initialize pages in dark mode
autoSwitchAppearance = false     # Ignore user's operating system/browser theme preferences
```

To remove the dark/light toggle icon from the site footer, configure `params.toml`:

```toml
[footer]
  showAppearanceSwitcher = false # Hides the switcher toggle button
```

---

## 7. Automated Content Engine: Multi-Language Translation Script

Nuno Coração automates the translation of his English articles into Portuguese (or any other language) using an offline NodeJS pipeline script. The setup reads source English markdowns, translates the body text via API, and writes translated copies into Hugo's respective language folders (e.g., `content.pt`).

### Step A: Configure Dependencies
Add `@iamtraction/google-translate` and `gray-matter` (for front-matter parsing) to your `package.json`:

```json
{
  "dependencies": {
    "@iamtraction/google-translate": "^2.2.0",
    "gray-matter": "^4.0.3"
  }
}
```
Install them locally using:
```bash
npm install
```

### Step B: Create the Translation Script (`genArticle.js`)
Create a JavaScript automation script (e.g., `genArticle.js` or `scripts/translate.js`) in the root directory to parse files, extract content, translate, and recompile front-matter:

```javascript
const fs = require('fs');
const path = require('path');
const matter = require('gray-matter');
const translate = require('@iamtraction/google-translate');

const SOURCE_DIR = path.join(__dirname, 'content/posts');
const TARGET_DIR = path.join(__dirname, 'content.pt/posts'); // For Portuguese locale

async function translateText(text, targetLang) {
    if (!text.trim()) return '';
    try {
        const res = await translate(text, { to: targetLang });
        return res.text;
    } catch (err) {
        console.error('Translation error:', err);
        return text;
    }
}

async function translateMarkdownFile(filePath, destPath) {
    const fileContent = fs.readFileSync(filePath, 'utf8');
    const { data: frontMatter, content } = matter(fileContent);

    // 1. Translate Front-matter fields (Title and Description)
    if (frontMatter.title) {
        frontMatter.title = await translateText(frontMatter.title, 'pt');
    }
    if (frontMatter.description) {
        frontMatter.description = await translateText(frontMatter.description, 'pt');
    }

    // 2. Translate Markdown Content Body (split by paragraphs to prevent length limits)
    const paragraphs = content.split('\n\n');
    const translatedParagraphs = [];
    for (const paragraph of paragraphs) {
        if (paragraph.trim().startsWith('```') || paragraph.trim().startsWith('![')) {
            // Keep code blocks and image references intact
            translatedParagraphs.push(paragraph);
        } else {
            const translated = await translateText(paragraph, 'pt');
            translatedParagraphs.push(translated);
        }
    }

    // 3. Rebuild the file
    const newMarkdown = matter.stringify(translatedParagraphs.join('\n\n'), frontMatter);
    
    // Ensure destination directory exists
    fs.mkdirSync(path.dirname(destPath), { recursive: true });
    fs.writeFileSync(destPath, newMarkdown, 'utf8');
    console.log(`Translated article saved to: ${destPath}`);
}

// Example Execution
const targetPost = 'hello-world/index.md';
translateMarkdownFile(
    path.join(SOURCE_DIR, targetPost),
    path.join(TARGET_DIR, targetPost)
);
```

You can execute this script during content creation to support multiple languages instantly:
```bash
node genArticle.js
```

---

## 8. Obsidian Integration Tips

If you plan on importing this guide (and `SETUP_LOG.md`) into your personal **Obsidian** vault:

1. **Obsidian Callouts:** The `> [!NOTE]` and `> [!IMPORTANT]` blocks used in this document are fully compatible with Obsidian's native Callouts styling out of the box.
2. **File Linking:** Ensure your Obsidian settings have *Use WikiLinks* toggled **ON** if you want to link between logs easily (e.g. `[[SETUP_LOG]]`).
3. **Fenced Code Blocks:** Keep code block structures intact inside Obsidian to ensure full syntax highlighting and copying functionality.
