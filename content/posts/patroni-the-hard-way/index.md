---
title: 
subtitle: ""
description: ""
slug: 
collections: 
date: 2025-05-04T14:23:57-04:00
keywords: 
tags: 
categories: 
featuredImage: cover.webp
featuredImagePreview: ""
draft: true
lightgallery: false
---

[High Avalaible Monolith Postgresql Cluster with Patroni&Consul&Haproxy | by Kaan Berk ÖZBEK | Apr, 2025 | Stackademic](https://blog.stackademic.com/high-avalaible-monolith-posgresql-cluster-with-patroni-consul-haproxy-42e37f27e383)

```
git clone https://github.com/msavdert/patroni-the-hard-way
cd patroni-the-hard-way/vagrant/RockyLinux9
vagrant up
vagrant ssh jumpbox
```

```sh
sudo su -
dnf update -y
```

## Change Timezone

```sh
timedatectl list-timezones | grep New
America/New_York

timedatectl set-timezone America/New_York
```

## Install and Configure NTP Service

[[ntp and chrony#Chrony]]

```sh
dnf install -y chrony
systemctl enable --now chronyd
systemctl status chronyd --no-pager
```

## Install prerequisites

```sh
curl -fsSL https://repo.pigsty.io/pig | bash
pig repo add pigsty pgdg infra -ru
pig ext install pg17-client -y
```

## Machine Database

```sh
vi machines.txt
```

```
192.168.56.11 db1.patroni.local db1
192.168.56.12 db2.patroni.local db2
192.168.56.13 db3.patroni.local db3
192.168.56.21 proxy1.patroni.local proxy1
192.168.56.22 proxy2.patroni.local proxy2
```

```sh
ssh-keygen -b 2048 -t rsa -f $HOME/.ssh/id_rsa -N ''
```

```sh
while read IP FQDN HOST; do
  if [ "$HOST" != "vip" ]; then
    ssh-copy-id root@${IP}
  fi
done < machines.txt
```

```sh
while read IP FQDN HOST; do
  if [ "$HOST" != "vip" ]; then
    ssh -n root@${IP} hostname
  fi
done < machines.txt
```

```
db1
db2
db3
proxy1
proxy2
```

## Hostnames

```sh
while read IP FQDN HOST SUBNET; do
    CMD="sed -i 's/^127.0.1.1.*/127.0.1.1\t${FQDN} ${HOST}/' /etc/hosts"
    ssh -n root@${IP} "$CMD"
    ssh -n root@${IP} hostnamectl set-hostname ${HOST}
    ssh -n root@${IP} systemctl restart systemd-hostnamed
done < machines.txt
```

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n root@${IP} hostname --fqdn
done < machines.txt
```

```
db1.patroni.local
db2.patroni.local
db3.patroni.local
proxy1.patroni.local
proxy2.patroni.local
```

## Host Lookup Table

```sh
echo "" > hosts
echo "# Patroni The Hard Way" >> hosts
```

```sh
while read IP FQDN HOST; do
    ENTRY="${IP} ${FQDN} ${HOST}"
    echo $ENTRY >> hosts
done < machines.txt
```

## Adding /etc/hosts Entries To A Local Machine

```sh
cat hosts >> /etc/hosts
```

```sh
for host in db1 db2 db3 proxy1 proxy2
   do ssh root@${host} hostname
done
```

```
db1
db2
db3
proxy1
proxy2
```

## Adding /etc/hosts Entries To The Remote Machines

```sh
while read IP FQDN HOST SUBNET; do
  scp hosts root@${HOST}:~/
  ssh -n \
    root@${HOST} "cat hosts >> /etc/hosts"
done < machines.txt
```

## Ensuring Time Synchronization with chrony

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "dnf install -y chrony && systemctl enable chronyd --now"
done < machines.txt
```

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "systemctl status chronyd --no-pager | grep Active"
done < machines.txt
```

## Disable and Stop Firewall Services

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "systemctl disable --now firewalld || true"
done < machines.txt
```

## Disable SELinux on All Nodes

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "setenforce 0 && sed -i 's/^SELINUX=.*/SELINUX=disabled/' /etc/selinux/config"
done < machines.txt
```

## Change Timezone on All Nodes

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "timedatectl set-timezone America/New_York"
done < machines.txt
```

```sh
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "date"
done < machines.txt
```

## Setting up the Distributed Configuration Store (etcd)

```sh
for host in db1 db2 db3; do
  ssh -n root@${host} "curl -fsSL https://repo.pigsty.io/pig | bash && \
    pig repo add pigsty pgdg infra -ru && \
    pig ext install etcd -y"
done
```

```sh
ssh db1 "etcd --version"
ssh db1 "etcdctl version"
```

```sh
for host in db1 db2 db3; do
  IP=$(grep ${host} machines.txt | cut -d' ' -f1)
  NAME=${host}
  CLUSTER_URL="db1=http://$(grep db1 machines.txt | cut -d' ' -f1):2380,db2=http://$(grep db2 machines.txt | cut -d' ' -f1):2380,db3=http://$(grep db3 machines.txt | cut -d' ' -f1):2380"
  cat << EOF | ssh root@${host} "cat > /etc/etcd/etcd.conf"
# [Member]
ETCD_LISTEN_PEER_URLS="http://$IP:2380"
ETCD_LISTEN_CLIENT_URLS="http://127.0.0.1:2379,http://$IP:2379"
ETCD_NAME="$NAME"
# [Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://$IP:2380"
ETCD_INITIAL_CLUSTER="$CLUSTER_URL"
ETCD_ADVERTISE_CLIENT_URLS="http://$IP:2379"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-1"
ETCD_INITIAL_CLUSTER_STATE="new"
# [Tune]
ETCD_ELECTION_TIMEOUT="5000"
ETCD_HEARTBEAT_INTERVAL="1000"
ETCD_INITIAL_ELECTION_TICK_ADVANCE="false"
ETCD_AUTO_COMPACTION_RETENTION="1"
EOF
done
```

```sh
for host in db1 db2 db3; do
  ssh -n root@${host} "systemctl daemon-reload && systemctl enable etcd --now"
done
```








































































































































```sh
for i in `vagrant status | grep running | cut -d " " -f1 | grep -v jumpbox`
do
  echo $i; vagrant ssh $i -- -t 'ip a' | grep "192.168.56" | cut -d / -f 1
done
```

```
db1
    inet 192.168.56.11
db2
    inet 192.168.56.12
db3
    inet 192.168.56.13
proxy1
    inet 192.168.56.21
proxy2
    inet 192.168.56.22
```

```
192.168.56.11 db1.patroni.local db1
192.168.56.12 db2.patroni.local db2
192.168.56.13 db3.patroni.local db3
192.168.56.21 proxy1.patroni.local proxy1
192.168.56.22 proxy2.patroni.local proxy2
```

resolv.conf

```
while read IP FQDN HOST SUBNET; do
  ssh -n \
    root@${HOST} "echo "nameserver 8.8.8.8" > /etc/resolv.conf"
done < machines.txt
```


```bash
for host in db1 db2 db3; do
  IP=$(grep ${host} machines.txt | cut -d' ' -f1)
  cat << EOF | ssh root@${host} "cat > /etc/consul.d/consul.hcl"
datacenter = "dc1"  
node_name = "${host}"  
bind_addr = "${IP}" # The IP address of this server  
client_addr = "0.0.0.0" # Allow Consul UI and agent to listen on all interfaces  
data_dir = "/var/lib/consul" # Data directory  
log_level = "INFO"  
  
server = true  
bootstrap_expect= 3 # Total number of Consul servers in the cluster  
# IP addresses of the other Consul servers  
retry_join = [
  "db1",
  "db2",
  "db3"
]
  
# (Optional) Enable the web UI  
ui_config {  
enabled = true  
}
EOF
done
```