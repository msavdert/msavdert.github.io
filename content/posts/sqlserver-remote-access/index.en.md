---
weight: 1
title: "How to configure remote access to a SQL Server"
date: 2024-03-22T12:48:41+08:00
#lastmod: 2020-03-03T16:29:41+08:00
draft: true
author: "Melih Savdert"
authorLink: "https://msavdert.github.io/"
description: "How to connect remotely to a SQL Server"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image.webp"

categories: ["SQL Server"]
tags: ["sqlserver", "mssql", "sql server - install and upgrade"]

lightgallery: true

toc:
  auto: false
---

In this article, I will explain step by step how to connect remotely to a SQL Server instance. Remote access is the ability to get access to a SQL Server from a remote distance in order to manipulate data which are located on that SQL Server.

<!--more-->

## Configuring remote access on a SQL Server instance

To enable remote connection on SQL Server right – click on the server and select the Properties option. In the Server Properties dialog under the Connections tab check the **Allow remote connections to this server** option:

![[Pasted image 20240321155156.png]]

![[Pasted image 20240321155446.png]]

## SQL Server Configuration Manager

Make sure that TCP/IP protocol is enabled and right click on TCP/IP and select the Properties option.

![[Pasted image 20240321155717.png]]

![[Pasted image 20240321155852.png]]

## Configure the Windows Firewall to allow SQL Server access

See the following example to open TCP port 1433 for SQL Server default instance.

Run Powershell as Administrator

```powershell
New-NetFirewallRule -DisplayName "SQLServer default instance" -Direction Inbound -LocalPort 1433 -Protocol TCP -Action Allow
```

## Change server authentication mode

1. In SQL Server Management Studio (SSMS) Object Explorer, right-click the server, and then select **Properties**.

2. On the **Security** page, under **Server authentication**, select the new server authentication mode, and then select **OK**.

3. In the SQL Server Management Studio dialog box, select **OK** to acknowledge the requirement to restart SQL Server.

4. In Object Explorer, right-click your server, and then select **Restart**. If SQL Server Agent is running, it must also be restarted.

![[Pasted image 20240321155156.png]]

![[Pasted image 20240321160703.png]]

![[Pasted image 20240321161146.png]]

## References:

- https://knowledgebase.apexsql.com/configure-remote-access-connect-remote-sql-server-instance-apexsql-tools/
- https://learn.microsoft.com/en-us/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access?view=sql-server-ver16
- https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/change-server-authentication-mode?view=sql-server-ver16