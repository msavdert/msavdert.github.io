---
weight: 1
title: "Install SQL Server and create a database on Red Hat"
date: 2024-03-14T14:54:41+08:00
#lastmod: 2020-03-03T16:29:41+08:00
draft: false
author: "Melih Savdert"
authorLink: "https://feelit.khusika.dev"
description: "FeelIt theme provides multiple shortcodes on top of built-in ones in Hugo."
resources:
- name: "featured-image"
  src: "https://unsplash.com/photos/macbook-pro-with-images-of-computer-language-codes-fPkvU7RDmCo"
- name: "featured-image-preview"
  src: "https://unsplash.com/photos/macbook-pro-with-images-of-computer-language-codes-fPkvU7RDmCo"

tags: ["mssql", "sqlserver", "linux"]
categories: ["SQL Server"]

lightgallery: true
---

In this quickstart, you install SQL Server 2022 (16.x) on Red Hat Enterprise Linux (RHEL) 8.x or 9.x. Then you can connect with sqlcmd to create your first database and run queries.

<!--more-->

## Prerequisites
You must have a RHEL 8.x machine with at least 2 GB of memory.

To install Red Hat Enterprise Linux on your own machine, go to https://access.redhat.com/products/red-hat-enterprise-linux/evaluation. You can also create RHEL virtual machines in Azure. See Create and Manage Linux VMs with the Azure CLI, and use --image RHEL in the call to az vm create.

If you've previously installed a Community Technology Preview (CTP) or Release Candidate (RC) of SQL Server, you must first remove the old repository before following these steps. For more information, see Configure repositories for installing and upgrading SQL Server on Linux.

For other system requirements, see System requirements for SQL Server on Linux.

## Install SQL Server

```bash
sudo yum install -y mssql-server
```

## Disable the sa account as a best practice

When you connect to your SQL Server instance using the sa account for the first time after installation, it's important for you to follow these steps, and then immediately disable the sa login as a security best practice.

1. Create a new login, and make it a member of the sysadmin server role.

* Depending on whether you have a container or non-container deployment, enable Windows authentication, and create a new Windows-based login and add it to the sysadmin server role.

 * Tutorial: Use adutil to configure Active Directory authentication with SQL Server on Linux

 * Tutorial: Configure Active Directory authentication with SQL Server on Linux containers

* Otherwise, create a login using SQL Server authentication, and add it to the sysadmin server role.

2. Connect to the SQL Server instance using the new login you created.

3. Disable the sa account, as recommended for security best practice.

## Install the SQL Server command-line tools

asdasd

## Create and query data

The following sections walk you through using sqlcmd to create a new database, add data, and run a simple query.

For more information about writing Transact-SQL statements and queries, see Tutorial: Writing Transact-SQL Statements.

### Create a new database

The following steps create a new database named TestDB.

1. From the sqlcmd command prompt, paste the following Transact-SQL command to create a test database:

```sql
CREATE DATABASE TestDB;
```

2. On the next line, write a query to return the name of all of the databases on your server:

### Insert data

Next create a new table, dbo.Inventory, and insert two new rows.

1. From the sqlcmd command prompt, switch context to the new TestDB database:
