---
title: "Proxmox VE 9.x Installation on Hetzner: A Complete ZFS Setup Guide"
date: 2026-05-05
draft: false
description: "A comprehensive walkthrough for installing Proxmox VE 9.x on a Hetzner Dedicated Server using a ZFS RAID0 layout, including QEMU-based GUI setup, SDN NAT networking, and Tailscale integration."
categories: ["Infrastructure"]
tags: ["proxmox", "hetzner", "zfs"]
---

Deploying Proxmox VE on a Hetzner dedicated bare metal server with a single public IP can be challenging because Hetzner does not provide standard IPMI/KVM access out of the box unless requested. 

In this guide, we will walk through the step-by-step process of downloading Proxmox VE 9.x, installing it via QEMU inside the Hetzner Rescue System using a **ZFS RAID0** layout for maximum performance across two disks, configuring native SDN-based NAT, and setting up Tailscale for secure remote management.

---

## 1. Booting into Hetzner Rescue System and Downloading the ISO

First, navigate to your **Hetzner Robot Panel**, activate the **Rescue System** (Linux, 64-bit), and reboot your server. Once logged into the rescue environment via SSH, fetch and download the latest Proxmox VE 9.x ISO:

```sh
# Fetch the latest ISO version name
ISO_VERSION=$(curl -s 'http://download.proxmox.com/iso/' | grep -oP 'proxmox-ve_(\d+.\d+-\d).iso' | sort -V | tail -n1)
ISO_URL="http://download.proxmox.com/iso/$ISO_VERSION"

# Download the ISO to the temp folder
curl $ISO_URL -o /tmp/proxmox-ve.iso

echo "Downloaded ISO version: $ISO_VERSION"
# Example output: proxmox-ve_9.1-1.iso
```

---

## 2. Acquiring Network Details from the Host

Before booting the virtual machine to start the installation, gather your main server network configuration details. We will need these parameters during the graphical setup:

```sh
# Determine interface name, IP, gateway and subnet
INTERFACE_NAME=$(udevadm info -q property /sys/class/net/eth0 | grep "ID_NET_NAME_PATH=" | cut -d'=' -f2)
IP_CIDR=$(ip addr show eth0 | grep "inet\b" | awk '{print $2}')
GATEWAY=$(ip route | grep default | awk '{print $3}')
IP_ADDRESS=$(echo "$IP_CIDR" | cut -d'/' -f1)
CIDR=$(echo "$IP_CIDR" | cut -d'/' -f2)

# Print values to write them down
echo "Interface: $INTERFACE_NAME" # e.g. enp0s31f6
echo "IP/CIDR:   $IP_CIDR"        # e.g. 192.0.2.113/26
echo "Gateway:   $GATEWAY"        # e.g. 192.0.2.65
echo "IP:        $IP_ADDRESS"     # e.g. 192.0.2.113
echo "CIDR:      $CIDR"           # e.g. 26
```

---

## 3. Initiating QEMU for Proxmox GUI Installation

Since we cannot boot the bare metal server directly into the Proxmox installer ISO, we will start a temporary virtual machine using QEMU. This VM will map the host's actual physical drives (`/dev/sda`, `/dev/sdb` etc.) to the VM's disk slots.

### Preparing UEFI Packages & Launching QEMU
Install `OVMF` for UEFI support, identify your primary and secondary disks, and spin up QEMU:

```sh
apt-get install -y ovmf

# Identify primary and secondary disks (filtering out loop/ram devices)
PRIMARY_DISK=$(lsblk -dn -o NAME,SIZE,TYPE -e 1,7,11,14,15 | sed -n 1p | awk '{print $1}')
SECONDARY_DISK=$(lsblk -dn -o NAME,SIZE,TYPE -e 1,7,11,14,15 | sed -n 2p | awk '{print $1}')

# Launch QEMU mapping both drives and the ISO
qemu-system-x86_64 -daemonize -enable-kvm -m 10240 -k en-us \
  -drive file=/dev/$PRIMARY_DISK,format=raw,media=disk,if=virtio,id=$PRIMARY_DISK \
  -drive file=/dev/$SECONDARY_DISK,format=raw,media=disk,if=virtio,id=$SECONDARY_DISK \
  -drive file=/usr/share/OVMF/OVMF_CODE.fd,if=pflash,format=raw,readonly=on \
  -drive file=/usr/share/OVMF/OVMF_VARS.fd,if=pflash,format=raw \
  -cdrom /tmp/proxmox-ve.iso -boot d \
  -vnc :0,password=on -monitor telnet:127.0.0.1:4444,server,nowait

# Set a secure VNC password to protect your installer session
echo "change vnc password YOUR_SECURE_VNC_PASSWORD" | nc -q 1 127.0.0.1 4444
```

### Accessing the Installer GUI
Open your VNC client (on macOS, press `CMD+K` in Finder) and connect using your server's public IP:
```text
vnc://192.0.2.113:5900
```
Enter the VNC password you set above (`YOUR_SECURE_VNC_PASSWORD`) to access the graphical installation wizard.

---

## 4. Walking Through the GUI Installer

1. Select **Install Proxmox VE (Graphical)**. 
   - *Note: A warning regarding missing hardware virtualization support is safe to ignore; it occurs because we are inside QEMU.*
2. Click **Agree** to the license agreement.
3. Under **Target Harddisk**, open **Options**:
   - Change filesystem to **zfs (RAID0)** to stripe across both disks for maximum performance.
   - Click **OK**.
4. Configure system parameters:
   - **Country:** United States (or your choice)
   - **Time zone:** UTC
   - **Password:** Enter a secure password (e.g. `YOUR_SECURE_ROOT_PASSWORD`)
   - **Email:** `your-email@example.com`
5. Configure management network settings. Enter the values you extracted from the host in Section 2:
   - **Management Interface:** Select the virtio interface.
   - **Hostname:** `pve.lan`
   - **IP Address:** `192.0.2.113/26` (Use your actual IP and CIDR)
   - **Gateway:** `192.0.2.65`
   - **DNS Server:** `1.1.1.1` (or `8.8.8.8`)
6. **Important:** **Untick** the *"Automatically reboot after successful installation"* box.
7. Click **Install**.

---

## 5. Post-Install Network Configuration (Within Rescue)

Once the installer finishes, **do not reboot the server** yet. Go back to your SSH session and stop QEMU:

```sh
# Gracefully stop the installer VM
printf "quit\n" | nc 127.0.0.1 4444
```

Now, launch QEMU again, but this time boot from the newly installed Proxmox OS on the virtual disk rather than the ISO, mapping port `22` in the VM to port `2222` on the host:

```sh
# Spin up QEMU booting from the virtual disk with port forwarding
qemu-system-x86_64 -daemonize -enable-kvm -m 10240 -k en-us \
  -drive file=/dev/$PRIMARY_DISK,format=raw,media=disk,if=virtio,id=$PRIMARY_DISK \
  -drive file=/dev/$SECONDARY_DISK,format=raw,media=disk,if=virtio,id=$SECONDARY_DISK \
  -drive file=/usr/share/OVMF/OVMF_CODE.fd,if=pflash,format=raw,readonly=on \
  -drive file=/usr/share/OVMF/OVMF_VARS.fd,if=pflash,format=raw \
  -vnc :0,password=on -monitor telnet:127.0.0.1:4444,server,nowait \
  -net user,hostfwd=tcp::2222-:22 -net nic

# Set the VNC password
echo "change vnc password YOUR_SECURE_VNC_PASSWORD" | nc -q 1 127.0.0.1 4444
```

Wait about a minute for Proxmox to boot inside the VM. On your host SSH terminal, install `sshpass` to easily copy configuration files into the virtual Proxmox host:

```sh
apt-get -y install sshpass
```

Write a proper bare metal network configuration file. Since Proxmox was installed inside QEMU, its network interface settings point to virtual devices. We need to restore the physical interface configuration so it can communicate on bare metal:

```sh
cat > /tmp/proxmox_network_config << EOF
auto lo
iface lo inet loopback
iface $INTERFACE_NAME inet manual

auto vmbr0
iface vmbr0 inet static
  address $IP_ADDRESS/$CIDR
  gateway $GATEWAY
  bridge_ports $INTERFACE_NAME
  bridge_stp off
  bridge_fd 0
EOF

# Copy the bare metal config file to the virtual Proxmox system
sshpass -p "YOUR_SECURE_ROOT_PASSWORD" scp -o StrictHostKeyChecking=no -P 2222 /tmp/proxmox_network_config root@localhost:/etc/network/interfaces

# Update nameserver inside Proxmox
sshpass -p "YOUR_SECURE_ROOT_PASSWORD" ssh -o StrictHostKeyChecking=no -p 2222 root@localhost "sed -i 's/nameserver.*/nameserver 1.1.1.1/' /etc/resolv.conf"
```

Once the network config is successfully updated, shutdown the QEMU VM:

```sh
printf "system_powerdown\n" | nc 127.0.0.1 4444
```

After the VM process exits, restart your Hetzner server to exit the Rescue System and boot into your fresh Proxmox bare metal hypervisor:

```sh
# Reboot host
shutdown -r now
```

---

## 6. Configuring Proxmox on Bare Metal

Once the host reboots, access the Proxmox Web GUI in your browser at `https://192.0.2.113:8006/` using root credentials.

### Running Helper Optimization Scripts
Open the Host Shell from the GUI and run the community post-installation script to clean up repositories, disable subscription warnings, and update package sources:

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/post-pve-install.sh)"
```

- Disabling the `pve-enterprise` repository: **Yes** (unless you have a subscription license).
- Correcting `ceph` package sources: **Yes**.
- Enabling the `pve-no-subscription` repository: **Yes**.
- Disabling the subscription nag popup: **Yes**.
- Updating packages and upgrading: **Yes**.

---

## 7. Creating an SDN NAT Zone and DHCP Range

Hetzner bonds public IPs to MAC addresses. To avoid paying for extra public IPs, we will configure **Software-Defined Networking (SDN)** to run VMs behind a NAT bridge.

### Enabling IP Forwarding (Kernel-level)
Execute the following commands in the host shell to allow the system to route traffic:

```bash
cat << EOF > /etc/sysctl.d/99-ip-forwarding.conf
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
EOF

# Apply the kernel configurations
sysctl -p /etc/sysctl.d/99-ip-forwarding.conf
```

Install the SDN controller dependencies:
```bash
apt update && apt install -y dnsmasq
systemctl disable --now dnsmasq # Proxmox will control it
```

Ensure your `/etc/network/interfaces` references the SDN configuration:
```bash
grep 'interfaces.d' /etc/network/interfaces || echo "source /etc/network/interfaces.d/*" >> /etc/network/interfaces
```

### Configuring SDN via Web UI

1. **Create the SDN Zone:**
   - Go to **Datacenter > SDN > Zones > Add > Simple**.
   - **ID:** `localnat`
   - **IPAM:** `pve`
   - **Advanced:** Check **automatic DHCP**.
2. **Create the VNet:**
   - Go to **Datacenter > SDN > VNets > Add**.
   - **Name:** `vnet0`
   - **Zone:** `localnat`
3. **Configure Subnet and SNAT:**
   - Click on your newly created `vnet0` and click **Create** under the *Subnets* panel.
   - **Subnet:** `10.0.0.0/24`
   - **Gateway:** `10.0.0.1`
   - **SNAT:** **Check this box** (crucial for internet access).
   - Under the **DHCP Ranges** tab, define the pool:
     - **Start Address:** `10.0.0.100`
     - **End Address:** `10.0.0.200`
4. **Apply Settings:**
   - Go back to the **Datacenter > SDN** page and click **Apply** at the top.

Now when you launch a VM or Container, choose `vnet0` as the bridge interface and set its network configuration to DHCP. It will automatically receive an IP in the `10.0.0.x` range and route to the internet.

---

## 8. Automating Proxmox with API Tokens

Instead of using the root credentials in Terraform, OpenTofu, or scripts, create a scoped API User:

```bash
# Add a new admin user
pveum user add admin@pve --comment "Automation Admin User"

# Grant administrative role on the entire cluster path (/)
pveum acl modify / --user admin@pve --role Administrator

# Generate an API Token
pveum user token add admin@pve mytoken --privsep 0
```

You can test authenticating with the token using `curl`:
```bash
curl -k -H 'Authorization: PVEAPIToken=admin@pve!mytoken=YOUR_API_TOKEN_VALUE' \
     https://100.64.0.10:8006/api2/json/nodes
```

---

## 9. Setting Up Tailscale VPN for Out-of-Band Access

To access your Proxmox dashboard without exposing public ports, configure Tailscale as a secure gateway:

```bash
# Download and install Tailscale
curl -fsSL https://tailscale.com/install.sh | sh

# Spin up Tailscale and advertise the private SDN NAT subnet
tailscale up --advertise-routes=10.0.0.0/24
```

### Optimizing Network Performance (UDP GRO forwarding)
Tailscale might warning you about suboptimal UDP configurations. Add persistent performance tuning to your interfaces:

```bash
# Apply dynamically
ethtool -K vmbr0 rx-udp-gro-forwarding on rx-gro-list off
```

To make it permanent, modify `/etc/network/interfaces` and add the `post-up` hook:

```text
auto vmbr0
iface vmbr0 inet static
  # ... your existing ip address configs ...
  post-up /usr/sbin/ethtool -K vmbr0 rx-udp-gro-forwarding on rx-gro-list off
```

Finally, go to your **Tailscale Admin Console**, click **Machines**, select your Proxmox host, click **Edit route settings**, and enable the `10.0.0.0/24` subnet route.

{{< alert type="note" >}}
**Health check warning:**
If you see the warning: *"Some peers are advertising routes but --accept-routes is false"*, you can resolve it by running:
```bash
sudo tailscale up --accept-routes
```
{{< /alert >}}

---

## References

- [Install Proxmox on a Hetzner Dedicated Server using SDN - CyanLabs](https://cyanlabs.net/tutorials/install-proxmox-on-a-hetzner-dedicated-server-with-1-ip-using-sdn-and-without-kvm-using-qemu/)
- [Proxmox VE Software-Defined Network Documentation](https://pve.proxmox.com/pve-docs/chapter-pvesdn.html)
