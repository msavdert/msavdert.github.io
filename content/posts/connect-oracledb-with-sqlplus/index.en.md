---
weight: 1
title: "Connect Oracle Database with sqlplus"
date: 2024-03-15T15:54:41+08:00
#lastmod: 2020-03-03T16:29:41+08:00
draft: false
author: "Melih Savdert"
authorLink: "https://msavdert.github.io/"
description: "Learn how to connect oracle database with sqlplus."
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image.webp"

tags: ["oracle", "sqlplus"]
categories: ["Oracle"]

lightgallery: true
---

In this article, you learn how to connect oracle database with sqlplus.

<!--more-->

## Connecting with Easy Connect Syntax

```bash
sqlplus username/password@host:port/service
```

```bash
sqlplus salesadmin@"dbhost.example.com/sales.example.com"
sqlplus salesadmin@"dbhost.example.com:1522/sales.example.com"
sqlplus salesadmin@"192.0.2.5/sales.example.com"
```

## TNS

```bash
"C:\app\client_home\bin\sqlplus.exe" username/password@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=host)(PORT=port))(CONNECT_DATA=(SERVER=DEDICATED)(SERVICE_NAME=service)))
```