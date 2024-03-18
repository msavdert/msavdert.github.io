---
weight: 1
title: "Convert your 19c non-CDB to PDB"
date: 2024-03-18T12:22:41+08:00
#lastmod: 2020-03-03T16:29:41+08:00
draft: false
author: "Melih Savdert"
authorLink: "https://msavdert.github.io/"
description: "Change the architecture of an Oracle database from the non-CDB architecture to the CDB."
resources:
- name: "featured-image"
  src: "featured-image.png"
- name: "featured-image-preview"
  src: "featured-image.png"

tags: ["mssql", "sqlserver", "linux"]
categories: ["SQL Server"]

lightgallery: true

toc:
  auto: false
---

You can change the architecture of an Oracle database from the non-CDB architecture to the Oracle multitenant architecture, also called the CDB architectur

<!--more-->

![[Pasted image 20240318121845.png]]
## Overview

In the following database conversion, we have a 19c database (non-CDB) ORA19.

In this article, I am assuming

1. We already have the CDB database up and running - ORACDB
2. We are going to convert non-cdb to cdb and plug it into the database.

**Create ORACDB Container Database**

```bash
dbca -silent -createDatabase \
-templateName General_Purpose.dbc \
-gdbName ORACDB \
-sid ORACDB \
-databaseType MULTIPURPOSE \
-createAsContainerDatabase true \
-numberOfPDBs 0 \
-characterSet AL32UTF8 \
-nationalCharacterSet AL16UTF16 \
-totalMemory 1024 \
-redoLogFileSize 10 \
-enableArchive true \
-storageType FS \
-datafileDestination /u01/app/oracle/oradata \
-recoveryAreaDestination /u01/app/oracle/fast_recovery_area \
-SysPassword Qazxsw123 \
-SystemPassword Qazxsw123 \
-emConfiguration none \
-sampleSchema false \
-useOMF true \
-initParams \
name='db_create_file_dest' value='/u01/app/oracle/oradata', \
name='db_create_online_log_dest_1' value='/u01/app/oracle/oradata', \
name='db_create_online_log_dest_2' value='/u01/app/oracle/fast_recovery_area', \
name='diagnostic_dest' value='/u01/app/oracle', \
name='parallel_max_servers' value=8, \
name='session_cached_cursors' value=200, \
name='processes' value=1500, \
name='open_cursors' value=1500
```

A very important file here to understand is the manifest.xml file. This file contains all information required to convert and migrate the non-container databases to container pluggable databases.

```bash
exec DBMS_PDB.DESCRIBE(pdb_descr_file => '/tmp/sncdb_manifest.xml');
```

This manifest XML structure consists of the following details -

1. Database version details
2. tablespace name and all its datafiles information
3. DB components information
4. DB parameters
5. SQL patches and opatch information
6. Time zone version
7. Services and DB edition

```text
<tablespace>
  <name>SYSAUX</name>
  <type>...</type>
  <status>...</status>
  ..
  ..
  ..
  ..
  <file>
    <path>...</path>
    <.....>
    <.....>
  </file>
  <file>
    <path>...</path>
    <.....>
    <.....>
  </file>
  ..
  ..
  ..
</tablespace>
```
## Step 1 - Perform clean shutdown at Non-CDB - sncdb
```bash
export ORACLE_SID=ORA19

sqlplus / as sysdba
shut immediate
```
## Step 2 - Start the Non-CDB sncdb database in READ ONLY mod
```sql
startup open read only
```
## Step 3 - Generate manifest XML file for ORA19 database
```sql
exec DBMS_PDB.DESCRIBE(pdb_descr_file => '/tmp/ora19_manifest.xml');
```
## Step 4 - Shut down the ORA19 database
```sql
shut immediate
```
> Note - From here, all steps will be performed on the Container database ORACDB
## Step 5 - Start dcdb database (container database) if not running
```sql
startup
```
## Step 6 - Check ORA19 (non-container database) compatibility with ORACDB (container database)
```bash
export ORACLE_SID=ORACDB
```
```sql
SET SERVEROUTPUT ON
DECLARE
compatible CONSTANT VARCHAR2(3) := CASE DBMS_PDB.CHECK_PLUG_COMPATIBILITY(pdb_descr_file => '/tmp/ora19_manifest.xml')
WHEN TRUE THEN 'YES'
ELSE 'NO'
END;
BEGIN
DBMS_OUTPUT.PUT_LINE(compatible);
END;
/
```

```text
YES

PL/SQL procedure successfully completed.
```
## Step 7 - Check for any errors resulting from the above step
```sql
col name for a10
col cause for a15
col type for a20
col message for a80
set lines 230

SELECT name, cause, type, message, status
 FROM PDB_PLUG_IN_VIOLATIONS
 WHERE name='ORA19';
```
	
```sql
NAME       CAUSE           TYPE                 MESSAGE                                                                          STATUS
---------- --------------- -------------------- -------------------------------------------------------------------------------- ---------
ORA19      Non-CDB to PDB  WARNING              PDB plugged in is a non-CDB, requires noncdb_to_pdb.sql be run.                  PENDING
```
> **Note:** If there are any errors, fix them before proceeding. I did not get any errors.

## Step 8 - Plug the ORA19 database into container ORACDB

```sql
create pluggable database ORA19 using '/tmp/ora19_manifest.xml' NOCOPY tempfile reuse;
```

```sql
SQL> show pdbs

    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         2 PDB$SEED                       READ ONLY  NO
         3 ORA19                          MOUNTED
```

> **Note:** below are some other options that can also be used with the "create pluggable database".

1. COPY - With this option, SNCDB will remain intact. All data files will remain untouched. For PDB files will be copied to the new location, provided with the parameter FILE_NAME_CONVERT.
2. NOCOPY - Existing files will be used and after completion of the operation, SNCDB will not remain usable. As new PDB is using the same data files.
3. MOVE - using parameter FILE_NAME_CONVERT, existing datafiles will be moved to the new location, hence after the operation completion, SNCDB will not be usable.

## Step 9 - Run the PDB conversion script provided by Oracle

```sql
alter session set container=ORA19;
@$ORACLE_HOME/rdbms/admin/noncdb_to_pdb.sql
```

## Step 10 - Start PDB and verify the state

```sql
alter pluggable database open;

show pdbs
```

```sql
    CON_ID CON_NAME                       OPEN MODE  RESTRICTED
---------- ------------------------------ ---------- ----------
         3 ORA19                          READ WRITE NO
```
## References

[How to convert non-cdb 19c to CDB/PDB 19c database (linkedin.com)](https://www.linkedin.com/pulse/how-convert-non-cdb-19c-cdbpdb-database-ravi-chauhan)

Best-practice Order for Multiple Changes Involving nonCDB to PDB Migration (Doc ID 2534697.1)

How to Convert Non-CDB to PDB - Step by Step Example (Doc ID 2288024.1)

How to Convert Non-CDB to PDB Database on same local host machine in 12c - Testcase (Doc ID 2012448.1)