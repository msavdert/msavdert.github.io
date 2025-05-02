---
title: "Plyr Video Player Kullanımı"
subtitle: "Hugo Sitelerinizde Profesyonel Video Oynatıcı"
date: 2025-05-01T10:00:00+03:00
lastmod: 2025-05-01T10:00:00+03:00
draft: false
author: "Melih Savdert"
authorLink: ""
description: "Hugo sitelerinizde Plyr video oynatıcısını nasıl kullanacağınızı öğrenin"
license: ""
images: []

tags: ["hugo", "video", "plyr", "shortcode"]
categories: ["Web Geliştirme"]

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

## Plyr Nedir?

Plyr, modern tarayıcıları destekleyen basit, hafif, erişilebilir ve özelleştirilebilir bir HTML5, YouTube ve Vimeo medya oynatıcısıdır. Sitenize profesyonel görünümlü video oynatıcıları kolayca ekleyebilmenize olanak tanır.

## Neden Plyr?

- 📼 HTML Video & Audio, YouTube & Vimeo - tüm popüler formatları destekler
- 💪 Erişilebilir - VTT altyazılar ve ekran okuyucular için tam destek
- 🔧 Özelleştirilebilir - oynatıcıyı istediğiniz şekilde görünecek şekilde düzenleyebilirsiniz
- 😎 Temiz HTML - doğru HTML elementleri kullanılmıştır
- 📱 Duyarlı - modern cihazlar için tam duyarlı tasarım

## Nasıl Kullanılır?

Sitenize Plyr video oynatıcıyı eklemek için oluşturduğumuz shortcode'u kullanabilirsiniz:

### 1. YouTube Videoları İçin

```go
{{</* plyr id="bTqVqk7FSmY" type="youtube" */>}}
```

Sonuç:

{{< plyr id="bTqVqk7FSmY" type="youtube" >}}

### 2. Vimeo Videoları İçin

```go
{{</* plyr id="76979871" type="vimeo" */>}}
```

Sonuç:

{{< plyr id="76979871" type="vimeo" >}}

### 3. Yerel MP4 Videoları İçin

```go
{{</* plyr id="migration-demo.mov" type="video" poster="poster.jpg" autoplay="true" */>}}
```

Eğer videoyu blog yazısının içine koymak isterseniz:

```go
{{</* plyr id="migration-demo.mov" type="video" */>}}
```

## Parametre Açıklamaları

Plyr shortcode'u aşağıdaki parametreleri desteklemektedir:

| Parametre | Açıklama | Varsayılan Değer |
| --------- | -------- | ---------------- |
| id | Video ID'si (YouTube/Vimeo) veya video dosya yolu | - |
| type | Video tipi: "youtube", "vimeo" veya "video" | "youtube" |
| autoplay | Otomatik oynatma: "true" veya "false" | "false" |
| poster | Video poster görüntüsü için URL (sadece yerel videolar için) | "" |
| class | Özel CSS sınıfı | "plyr-player" |

## Özelleştirme

Plyr oynatıcınızı özelleştirmek isterseniz, `layouts/partials/custom/head.html` dosyasında CSS değişkenlerini düzenleyebilirsiniz:

```css
:root {
    --plyr-color-main: #0077CC; /* Ana renk */
    --plyr-video-controls-background: rgba(0, 0, 0, 0.8); /* Kontrol paneli arkaplanı */
    --plyr-audio-controls-background: #fff; /* Ses kontrolleri arkaplanı */
}
```

## Sonuç

Bu basit shortcode ile sitenize profesyonel görünümlü video oynatıcıları kolayca ekleyebilirsiniz. Plyr, YouTube ve Vimeo videolarının yanı sıra yerel video dosyalarını da desteklediğinden dolayı, tüm video ihtiyaçlarınız için tek bir çözüm sunar.

Daha fazla bilgi için [Plyr'ın resmi sitesini](https://plyr.io) ziyaret edebilirsiniz.