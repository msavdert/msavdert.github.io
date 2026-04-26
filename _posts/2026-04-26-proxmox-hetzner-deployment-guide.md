---
title: "Proxmox VE on Hetzner Bare Metal: The Complete Deployment Guide"
date: 2026-04-26 10:00:00 +0000
categories: [Virtualization, Hetzner]
tags: [proxmox, hetzner, sdn]
---

Deploying Proxmox VE on a Hetzner Dedicated Server can be a bit tricky due to Hetzner's specific network security policies. In this guide, we will walk through the process of installing Proxmox VE on a fresh Debian 13 (Trixie) installation and configuring a modern, scalable networking stack using Software-Defined Networking (SDN).

If you are looking to expose your Proxmox dashboard securely without opening ports, check out my follow-up guide: [Securing Proxmox VE with Cloudflare Tunnel](/posts/securing-proxmox-with-cloudflare-tunnel/).

## 1. Initial Installation via Hetzner Rescue System

First, we need to boot into the Hetzner Rescue System to install a clean Debian base.

1. Go to the [Hetzner Robot](https://robot.hetzner.com/server) panel.
2. Select your server -> **Rescue** tab.
3. Select **Linux** as the operating system and activate the rescue system.
4. Note down the temporary root password and reboot the server.

Connect via SSH:
```bash
ssh root@<SERVER_IP>
```

Run the [installimage](https://docs.hetzner.com/robot/dedicated-server/operating-systems/installimage/) script:
```bash
installimage
```
Choose **Debian 13 (Trixie)**. You can leave most settings as default, but ensure your disk layout (RAID/LVM) matches your needs. After the installation completes, reboot the server.

## 2. Installing Proxmox VE

Once logged back into the fresh Debian system, we need to add the Proxmox repositories.

### Repository Configuration
Create the source list for Proxmox VE:
```bash
cat > /etc/apt/sources.list.d/pve-install-repo.sources << EOL
Types: deb
URIs: http://download.proxmox.com/debian/pve
Suites: trixie
Components: pve-no-subscription
Signed-By: /usr/share/keyrings/proxmox-archive-keyring.gpg
EOL

wget https://enterprise.proxmox.com/debian/proxmox-archive-keyring-trixie.gpg -O /usr/share/keyrings/proxmox-archive-keyring.gpg
```

### System Upgrade & Kernel Installation
Update the system and install the Proxmox kernel:
```bash
apt update && apt full-upgrade
apt install proxmox-default-kernel
systemctl reboot
```

### Core Packages
After rebooting into the Proxmox kernel, install the VE environment:
```bash
apt install proxmox-ve postfix open-iscsi chrony
apt remove linux-image-amd64 'linux-image-6.12*'
update-grub
apt update && apt full-upgrade
passwd root
```

You can now access the Proxmox Web GUI at `https://<SERVER_IP>:8006/`.

## 3. Network Configuration: The Modern Way

Hetzner enforces strict IP/MAC binding. If you have only one Public IP, you cannot use a standard Bridged setup for your VMs without purchasing additional IPs and MAC addresses. 

Instead, we will use **Masquerading (NAT)** with the new **Software-Defined Network (SDN)** feature in Proxmox. This allows your VMs to sit behind the host's IP, which is both cost-effective and secure.

### Enable IP Forwarding
The host must act as a router for the VMs:
```bash
cat << EOF > /etc/sysctl.d/ip-forwarding.conf
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
EOF

systemctl restart systemd-sysctl
```

### Install SDN Dependencies

The [Software-Defined Network](https://pve.proxmox.com/pve-docs/chapter-pvesdn.html) (SDN) feature in Proxmox VE enables the creation of virtual zones and networks (VNets). This functionality simplifies advanced networking configurations and multitenancy setup.

```bash
apt update && apt install -y libpve-network-perl dnsmasq
systemctl disable --now dnsmasq # Proxmox manages this
```

Ensure your `/etc/network/interfaces` includes the `interfaces.d` directory:
```bash
grep 'interfaces.d' /etc/network/interfaces || echo "source /etc/network/interfaces.d/*" >> /etc/network/interfaces
```

### Configure SDN in Proxmox GUI

1. **Create a Zone**: `Datacenter > SDN > Zones > Add > Simple`.
   - **ID**: `hnat`
   - **IPAM**: `pve`
   - **Advanced**: Check `automatic DHCP`.
2. **Create a VNet**: `Datacenter > SDN > VNets > Add`.
   - **Name**: `vnet1`
   - **Zone**: `hnat`
3. **Add a Subnet**: Click on `vnet1` and create a new Subnet.
   - **Subnet**: `10.0.0.0/24`
   - **Gateway**: `10.0.0.1`
   - **DHCP Ranges**: `10.0.0.100 - 10.0.0.200`
   - **SNAT**: Check this (CRITICAL for internet access).
4. **Apply**: Go back to the SDN section and click **Apply**.

Now, when you create a VM or Container, select `vnet1` as the bridge and set IPv4 to `DHCP`.

## 4. Post-Installation Hardening

### Fail2Ban
Protect your SSH service from brute-force attacks:
```bash
apt update && apt install fail2ban -y
cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```
Edit `/etc/fail2ban/jail.local` and ensure `[sshd]` is enabled:
```ini
[sshd]
enabled = true
```
Restart the service:
```bash
systemctl restart fail2ban
```

### Hetzner Firewall
In the [Hetzner Robot Panel](https://robot.hetzner.com/server), go to **Firewall** and apply the `SSH` template. This ensures that only essential ports are open at the edge.

### Proxmox Helper Scripts
For quick optimizations, use the excellent community scripts:
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

## Conclusion

You now have a fully functional Proxmox VE node running on Hetzner with a modern SDN-based NAT network. This setup is flexible, secure, and doesn't require additional IP costs.

In the next post, we'll look at [how to securely access this dashboard using Cloudflare Tunnels](/posts/securing-proxmox-with-cloudflare-tunnel/).

## References

- [Install Proxmox VE on Debian 13 Trixie - Proxmox VE](https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_13_Trixie)
- [Install and Configure Proxmox VE | Hetzner Community](https://community.hetzner.com/tutorials/install-and-configure-proxmox_ve)
- [Install Proxmox on a Hetzner Dedicated Server with 1 IP using SDN and without KVM using QEMU - CyanLabs](https://cyanlabs.net/tutorials/install-proxmox-on-a-hetzner-dedicated-server-with-1-ip-using-sdn-and-without-kvm-using-qemu/)