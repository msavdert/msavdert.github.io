---
title: "Using Plyr Video Player"
subtitle: "Professional Video Player for Your Hugo Sites"
date: 2025-05-01T10:00:00+03:00
lastmod: 2025-05-01T10:00:00+03:00
draft: false
author: "Melih Savdert"
authorLink: ""
description: "Learn how to use the Plyr video player in your Hugo sites"
license: ""
images: []

tags: ["hugo", "video", "plyr", "shortcode"]
categories: ["Web Development"]

featuredImage: "plyr-featured.jpg"
featuredImagePreview: "plyr-featured.jpg"

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
fontawesome: true
linkToMarkdown: true
rssFullText: false

toc:
  enable: true
  auto: true
code:
  copy: true
  maxShownLines: 50
---

## What is Plyr?

Plyr is a simple, lightweight, accessible, and customizable HTML5, YouTube, and Vimeo media player that supports modern browsers. It allows you to easily add professional-looking video players to your site.

## Why Choose Plyr?

- 📼 HTML Video & Audio, YouTube & Vimeo - supports all popular formats
- 💪 Accessible - full support for VTT captions and screen readers
- 🔧 Customizable - you can style the player to appear exactly how you want
- 😎 Clean HTML - uses the correct HTML elements
- 📱 Responsive - fully responsive design for modern devices

## How to Use It

You can use the shortcode we've created to add the Plyr video player to your site:

### 1. For YouTube Videos

```plain
{{</* plyr id="bTqVqk7FSmY" type="youtube" */>}}
```

Result:

{{< plyr id="bTqVqk7FSmY" type="youtube" >}}

### 2. For Vimeo Videos

```plain
{{</* plyr id="76979871" type="vimeo" loop="true" */>}}
```

Result:

{{< plyr id="76979871" type="vimeo" loop="true" >}}

### 3. For Local MP4/MOV Videos

```plain
{{</* plyr id="/videos/my-video.mp4" type="video" poster="poster.jpg" autoplay="true" */>}}
```

Simple local video example:

```plain
{{</* plyr id="migration-demo.mov" type="video" */>}}
```

Result:

{{< plyr id="migration-demo.mov" type="video" >}}

## Advanced Features

Our enhanced Plyr shortcode supports many advanced features:

### With Subtitles/Captions

```plain
{{</* plyr 
  id="migration-demo.mov" 
  type="video" 
  captions="sample-captions.vtt"
  captionsLang="en"
  captionsLabel="English" 
*/>}}
```

### Multiple Video Quality Options

```plain
{{</* plyr 
  id="high-quality.mp4,medium-quality.mp4,low-quality.mp4" 
  type="video" 
  quality="1080p,720p,480p" 
*/>}}
```

### Video Chapters

```plain
{{</* plyr 
  id="migration-demo.mov" 
  type="video" 
  chapters="video-chapters.vtt" 
*/>}}
```

### With Preview Thumbnails

```plain
{{</* plyr 
  id="migration-demo.mov" 
  type="video" 
  previewThumbnails="thumbnails.vtt" 
*/>}}
```

### Multiple Tracks (Subtitles, Descriptions)

```plain
{{</* plyr 
  id="migration-demo.mov" 
  type="video"
  tracks="english.vtt,french.vtt,audio-description.vtt" 
  trackLabels="English,Français,Audio Description" 
  trackLangs="en,fr,en" 
  trackKinds="captions,captions,descriptions" 
*/>}}
```

### With Custom Content

```plain
{{</* plyr id="migration-demo.mov" type="video" */>}}
**Video Title**: Database Migration Demo
*Recorded on May 1, 2025*

This video demonstrates how to perform a database migration.
{{</* /plyr */>}}
```

Result:

{{< plyr id="migration-demo.mov" type="video" >}}
**Video Title**: Database Migration Demo  
*Recorded on May 1, 2025*

This video demonstrates how to perform a database migration.
{{< /plyr >}}

## Complete Parameter List

The enhanced Plyr shortcode supports the following parameters:

| Parameter | Description | Default Value |
| --------- | ----------- | ------------- |
| id | Video ID (YouTube/Vimeo) or video file path | - |
| type | Video type: "youtube", "vimeo" or "video" | "youtube" |
| autoplay | Auto play: "true" or "false" | "false" |
| poster | URL for video poster image (local videos only) | "" |
| class | Custom CSS class | "plyr-player" |
| captions | URL to captions/subtitle file (.vtt format) | "" |
| captionsLang | Language code for captions | "en" |
| captionsLabel | Display label for captions | "English" |
| tracks | Comma-separated list of additional .vtt track files | "" |
| trackLabels | Comma-separated list of labels for tracks | "" |
| trackLangs | Comma-separated list of language codes for tracks | "" |
| trackKinds | Comma-separated list of track types (captions,chapters,descriptions) | "" |
| previewThumbnails | URL to preview thumbnails file (.vtt or .jpg) | "" |
| chapters | URL to chapters file (.vtt format) | "" |
| quality | For local videos: comma-separated quality labels | "" |
| speed | Enable speed controls: "true" or "false" | "true" |
| loop | Loop the video: "true" or "false" | "false" |
| hideControls | Auto-hide controls: "true" or "false" | "false" |
| muted | Start with audio muted: "true" or "false" | "false" |
| clickToPlay | Allow clicking on the video to play/pause: "true" or "false" | "true" |

## Creating VTT Files

For subtitles, chapters and thumbnails you need to create VTT files. Here's a simple example:

### Subtitles (captions.vtt)

```plain
WEBVTT

00:00:01.000 --> 00:00:04.000
Welcome to this video tutorial.

00:00:05.000 --> 00:00:07.000
Today we'll learn about database migrations.
```

### Chapters (chapters.vtt)

```plain
WEBVTT

00:00:00.000 --> 00:00:30.000
Introduction

00:00:30.000 --> 00:01:30.000
Database Setup

00:01:30.000 --> 00:03:00.000
Migration Process
```

## Customization

If you want to customize your Plyr player, you can edit the CSS variables in the `layouts/partials/custom/head.html` file:

```css
:root {
    --plyr-color-main: #0077CC; /* Main color */
    --plyr-video-controls-background: rgba(0, 0, 0, 0.8); /* Control panel background */
    --plyr-audio-controls-background: #fff; /* Audio controls background */
}
```

## Conclusion

With this enhanced shortcode, you can easily add professional-looking video players to your site with advanced features like subtitles, multiple quality options, chapters, and more. Plyr supports YouTube and Vimeo videos as well as local video files, providing a powerful solution for all your video needs.

For more information, visit the [official Plyr website](https://plyr.io).