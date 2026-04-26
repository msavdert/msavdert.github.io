---
title: "Securing Proxmox VE with Cloudflare Tunnel: A Zero Trust Approach"
date: 2026-04-26 12:30:00 +0000
categories: [Security, Networking]
tags: [cloudflare, zero-trust, proxmox, cloudflared]
---

In my [previous post](/posts/proxmox-hetzner-deployment-guide/), we set up Proxmox VE on a Hetzner dedicated server. While the installation is complete, accessing the Proxmox dashboard usually involves opening port `8006` to the public internet or using a VPN. 

A more modern and secure approach is to use **Cloudflare Tunnels (Cloudflared)**. This allows you to access your dashboard via a nice hostname (e.g., `proxmox.yourdomain.com`) without opening any inbound ports on your server.

## Why Cloudflare Tunnels?

1. **No Inbound Ports**: You can keep your firewall completely closed to the public internet.
2. **Identity-Based Access**: You can add Cloudflare Access rules (SSO, 2FA) in front of your Proxmox login.
3. **Automated SSL**: Cloudflare handles the SSL/TLS certificates automatically.

## 1. Prerequisites

- A Proxmox VE server (see the [installation guide](/posts/proxmox-hetzner-deployment-guide/)).
- A domain managed by Cloudflare.
- A Cloudflare Zero Trust account (Free tier is fine).

## 2. Setting Up the Tunnel in Cloudflare Dashboard

1. Log in to the [Cloudflare Dashboard](https://dash.cloudflare.com/).
2. Navigate to **Zero Trust > Networks > Tunnels**.
3. Click **Create a tunnel** and name it (e.g., `proxmox-hetzner`).
4. Select **Cloudflared** as the connector.
5. Choose your environment (e.g., **Debian 64-bit**).

## 3. Installing Cloudflared on Proxmox

Back in your Proxmox terminal, follow these steps to install the agent:

### Add Cloudflare Repository
```bash
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-public-v2.gpg | sudo tee /usr/share/keyrings/cloudflare-public-v2.gpg >/dev/null

echo 'deb [signed-by=/usr/share/keyrings/cloudflare-public-v2.gpg] https://pkg.cloudflare.com/cloudflared any main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt-get update && sudo apt-get install cloudflared
```

### Install the Service
Cloudflare will provide you with a command containing a unique token. Run it on your server:
```bash
sudo cloudflared service install <YOUR_TUNNEL_TOKEN>
```

Enable and start the service:
```bash
systemctl enable cloudflared
systemctl start cloudflared
```

## 4. Configuring Public Hostnames

In the Cloudflare Dashboard, under your tunnel configuration:

1. Go to the **Public Hostname** tab.
2. Click **Add a public hostname**.
3. Fill in the details:
   - **Subdomain**: `proxmox`
   - **Domain**: `yourdomain.com`
   - **Service Type**: `HTTPS`
   - **URL**: `localhost:8006`
4. **Important**: Since Proxmox uses a self-signed certificate by default, go to **Additional application settings > HTTP Settings** and enable **No TLS Verify**.

## 5. Fixing QUIC Handshake Timeouts

If you are using the Hetzner Firewall, you might encounter `QUIC handshake timeout` errors because Cloudflare Tunnel uses UDP for its high-performance transport.

To fix this, go to your [Hetzner Robot Panel](https://robot.hetzner.com/server) and add a firewall rule:

- **Name**: `Cloudflare QUIC`
- **Protocol**: `UDP`
- **Source Port**: `0-65535`
- **Destination Port**: `32768-65535`
- **Action**: `Accept`

Apply and save the rules.

## 6. Verification

Restart the service and check the status:
```bash
systemctl restart cloudflared
systemctl status cloudflared
```

You should now be able to access your Proxmox GUI at `https://proxmox.yourdomain.com`. 

## Next Steps

Now that your dashboard is secure, I highly recommend setting up **Cloudflare Access** policies to restrict access only to your email address or specific IP ranges. This adds a powerful layer of security before someone even sees the Proxmox login screen.

---
*If you missed the initial server setup, check out the [Hetzner Proxmox Deployment Guide](/posts/proxmox-hetzner-deployment-guide/).*

## References

- [Resolved: Connection Issues with Cloudflared due to Ingress UDP Traffic - SSE and Zero trust / Cloudflare Tunnel - Cloudflare Community](https://community.cloudflare.com/t/resolved-connection-issues-with-cloudflared-due-to-ingress-udp-traffic/515241)
- [Troubleshooting · Cloudflare Docs](https://developers.cloudflare.com/tunnel/troubleshooting/#quic-handshake-timeout)
