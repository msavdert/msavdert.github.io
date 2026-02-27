---
title: "Setting Up GitHub Pages with Custom Subdomain Using Cloudflare DNS"
date: 2026-02-26 11:00:00 +0000
categories: [Web Development, GitHub Pages]
tags: [github-pages, cloudflare, dns, custom-domain, subdomain]
---

# GitHub Pages & Cloudflare DNS Setup for Subdomains

This guide provides the official, recommended steps to configure a custom subdomain (e.g., `blog.savdert.com`) for GitHub Pages using Cloudflare as your DNS provider, based on GitHub's official documentation.

## 1. Configure GitHub Pages

First, you need to tell your GitHub repository that you intend to use a custom domain.

1. Navigate to your GitHub repository.
2. Go to **Settings > Pages** (under the "Code and automation" section).
3. Under **Custom domain**, type your subdomain (e.g., `blog.savdert.com`).
4. Click **Save**.
   - *Note: This will automatically create a commit adding a `CNAME` file to the root of your repository.*

### Add a DNS TXT record (Verification Steps)

1. Create a TXT record in your DNS configuration for the following hostname: `_github-pages-challenge-msavdert.blog.savdert.com`
2. Use this code for the value of the TXT record: `12bbba65edb51cea2861f647fe3d27`
3. Wait until your DNS configuration changes. This could take up to 24 hours to propagate.

## 2. Configure Cloudflare DNS (CNAME Record)

For a subdomain (like `blog.savdert.com`), GitHub requires a **CNAME** record, not A records.

1. Go to your Cloudflare Dashboard and navigate to **DNS > Records**.
2. Add a new record with the following details:
   - **Type:** `CNAME`
   - **Name:** `blog` (the subdomain part) and `www.blog`
   - **Target:** `msavdert.github.io` (your GitHub Pages default domain)
   - **Proxy status:** ☁️ **DNS Only** (Grey cloud)
     - *Important: Leave it as "DNS Only" initially so GitHub can successfully provision an SSL certificate. You can turn on the orange cloud (Proxied) later if you want Cloudflare's features.*

> [!warning] Security Warning
> GitHub strongly recommends **never** using wildcard DNS records (e.g., `*.savdert.com`), as they put you at immediate risk of domain takeovers.

## 3. Verify Your Custom Domain (Highly Recommended)

Verifying your domain at the account level prevents other GitHub users from taking over your custom domain if you ever delete your repository or remove the custom domain from your repository settings.

1. In the upper-right corner of GitHub, click your profile picture and go to **Settings**.
2. In the sidebar under "Code, planning, and automation", click **Pages**.
3. Click **Add a domain**.
4. Enter `blog.savdert.com` and click **Add domain**.
5. GitHub will provide a **DNS TXT record** to add (e.g., `_github-pages-challenge-msavdert`).
6. Go back to **Cloudflare DNS** and add the TXT record:
   - **Type:** `TXT`
   - **Name:** `_github-pages-challenge-msavdert` (or exactly as GitHub instructs)
   - **Content:** The challenge code provided by GitHub.
7. Return to GitHub and click **Verify**. (DNS changes might take a few minutes).

## 4. Enforce HTTPS and SSL Settings

GitHub Pages automatically provisions HTTPS certificates for custom domains.

1. Go back to your repository's **Settings > Pages**.
2. Check the **Enforce HTTPS** box. *(This might take up to 24 hours to become available after DNS changes).*
3. **Cloudflare SSL/TLS Settings:**
   - If you decide to enable Cloudflare's Proxy (Orange cloud) later, ensure your SSL/TLS encryption mode in Cloudflare is set to **Full** or **Full (Strict)** to avoid redirect loops. Do not use "Flexible".

---

**Official References:**

- [Managing a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain)
- [Verifying your custom domain for GitHub Pages](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages)