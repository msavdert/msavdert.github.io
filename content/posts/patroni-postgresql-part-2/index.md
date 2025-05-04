---
title: "PostgreSQL High Availability with Patroni - Part 2: Configuration and Failover"
date: 2025-05-04 # Gerekirse tarihi güncelleyebilirsiniz
tags: ["postgresql", "patroni", "high availability", "database", "configuration"]
categories: ["Databases"]
collections: ["PostgreSQL High Availability with Patroni"] # Changed from series to collections
# featuredImage: "featured-image.png"
---

## Patroni Configuration and Failover Testing

Welcome to the second part of the PostgreSQL High Availability with Patroni collection. Here, we will focus on:

*   Configuring Patroni (YAML configuration)
*   Configuring DCS (etcd, Consul, ZooKeeper)
*   Starting the cluster
*   Testing automatic failover
*   Manual switchover/failover procedures

Let's configure our cluster for resilience.
