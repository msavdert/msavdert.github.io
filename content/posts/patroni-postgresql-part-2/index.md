---
title: "PostgreSQL High Availability with Patroni - Part 2: Configuration and Failover"
subtitle: Cluster Configuration and Failover Testing
description: Configuring a Patroni cluster with a Distributed Configuration Store (DCS), starting the cluster, and testing automatic/manual failover procedures.
slug: 
collections: PostgreSQL High Availability with Patroni
date: 2025-05-04T02:32:07-04:00
keywords:
  - postgresql
  - postgres
  - patroni
  - high availability
  - database
tags:
  - postgresql
  - patroni
  - high-availability
  - database
categories: PostgreSQL
images: 
featuredImage: featured-image.webp
featuredImagePreview: ""
draft: false
lightgallery: false
---

## Patroni Configuration and Failover Testing

Welcome to the second part of the PostgreSQL High Availability with Patroni collection. Here, we will focus on:

*   Configuring Patroni (YAML configuration)
*   Configuring DCS (etcd, Consul, ZooKeeper)
*   Starting the cluster
*   Testing automatic failover
*   Manual switchover/failover procedures

Let's configure our cluster for resilience.
