---
title: "Getting Started with Pigsty: Self-Hosting PostgreSQL Like a Pro"
date: 2026-02-26 10:00:00 +0000
categories: [Database, PostgreSQL]
tags: [postgresql, pigsty, self-hosting, rds]
---

# Introduction to Pigsty

Pigsty is an open-source, battery-included PostgreSQL distribution that allows you to self-host PostgreSQL databases like a professional RDS (Relational Database Service). Developed by Vonng, Pigsty provides a comprehensive platform for deploying, managing, and monitoring PostgreSQL clusters with enterprise-grade features.

## What is Pigsty?

Pigsty stands for "PostgreSQL In Great STYle" and encompasses Postgres, Infrastructure, Graphics, Service, Toolbox, and more. It's designed to make PostgreSQL deployment as easy as possible while offering advanced capabilities that rival commercial database services.

Key features include:
- **Extensible**: Supports 451+ PostgreSQL extensions
- **Reliable**: Self-healing HA clusters with PITR and SSL security
- **Observable**: Built-in monitoring with Victoria Metrics and Grafana
- **Scalable**: Auto-routed database services with connection pooling
- **Maintainable**: One-command deployment and IaC support
- **Composable**: Modular design with bonus modules like Redis, MinIO, and more

## Why Choose Pigsty for PostgreSQL?

Traditional PostgreSQL setup requires extensive configuration for high availability, monitoring, and security. Pigsty simplifies this by providing:
- Pre-configured HA clusters using Patroni and etcd
- Point-in-time recovery with pgBackRest
- Comprehensive monitoring dashboards
- Automatic service discovery and load balancing
- Security best practices out of the box

## Getting Started with Pigsty

### Prerequisites

Pigsty runs on bare Linux systems. Supported distributions include:
- Rocky Linux / AlmaLinux / RHEL 8/9/10
- Ubuntu 22.04/24.04
- Debian 12/13

You'll need a fresh Linux node (physical or virtual) with:
- x86_64 or aarch64 architecture
- At least 2GB RAM (4GB recommended)
- SSH access with sudo privileges

### Installation

1. **Download and run the installer**:
   ```bash
   curl -fsSL https://repo.pigsty.io/get | bash
   cd ~/pigsty
   ```

2. **Configure your deployment**:
   ```bash
   ./configure -g  # Generate config with random passwords
   ```

3. **Deploy Pigsty**:
   ```bash
   ./deploy.yml  # Deploy everything on the current node
   ```

After deployment, you'll have:
- PostgreSQL running on port 5432
- Web UI available at http://your-ip (ports 80/443)
- Monitoring dashboards at http://your-ip:3000

### Creating Your First Database

Connect to PostgreSQL:
```bash
psql -h your-ip -U dbuser_dba -d postgres
```

Create a database and user:
```sql
CREATE DATABASE myapp;
CREATE USER myuser WITH PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE myapp TO myuser;
```

### Advanced Setup: HA Cluster

For production environments, deploy multi-node clusters:

1. Prepare multiple nodes with Pigsty installed
2. Configure cluster topology in `pigsty.yml`
3. Run the deployment playbook

Example 3-node cluster configuration:
```yaml
pg-test:
  hosts:
    10.10.10.11: { pg_seq: 1, pg_role: primary }
    10.10.10.12: { pg_seq: 2, pg_role: replica }
    10.10.10.13: { pg_seq: 3, pg_role: offline }
  vars: { pg_cluster: pg-test }
```

Deploy with:
```bash
bin/pgsql-add pg-test
```

## PostgreSQL-Specific Features in Pigsty

Pigsty enhances PostgreSQL with:
- **Extension Management**: Easy installation of popular extensions like PostGIS, TimescaleDB, etc.
- **Performance Tuning**: Auto-tuning based on hardware and workload
- **Backup & Recovery**: Integrated PITR with optional S3/MinIO storage
- **Security**: Pre-configured SSL, HBA rules, and access controls
- **Monitoring**: Detailed metrics for PostgreSQL performance, queries, and system health

## Conclusion

Pigsty transforms PostgreSQL from a powerful database engine into a complete database platform. Whether you're a developer looking for a local PostgreSQL setup or an organization needing production-grade database infrastructure, Pigsty provides the tools and automation to get started quickly and maintain reliably.

For more information, visit the [Pigsty documentation](https://pigsty.io/docs/) or check out the [GitHub repository](https://github.com/pgsty/pigsty).