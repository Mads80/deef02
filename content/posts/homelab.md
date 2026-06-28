+++
date = '2026-06-26T12:56:50+02:00'
draft = true
title = 'Homelabbing'
tags = ['homelab', 'proxmox', 'raspberry pi', 'home assistant', 'pi-hole', 'reverse proxy', 'nginx', 'npm', 'unifi', 'tls', 'dns']
+++

## Overview

Personal documentation for my homelab infrastructure. This documentation contains a consolidated overview of infrastructure, network topology and links to installation scripts.

---

## Hardware

> - GeeekPi DeskPi RackMate T1
> - Lenovo ThinkCentre M920x, i5-9400T, 16 GB RAM, 256 GB NVMe (lv-426)
> - Lenovo ThinkCentre M920x, i5-8500, 16 GB RAM, 256 GB NVMe (lv-223)
> - Lenovo ThinkCentre M920x, i3-8100, 16 GB RAM, 256 GB NVMe (lv-178)
> - Raspberry Pi 3B + HiFiBerry AMP+
> - Raspberry Pi 2B
> - Cloud Gateway Ultra
> - USW Flex Mini
> - U7 Lite AP

![My Homelab](/images/IMG_7091.jpg "My Homelab")
<!-- Farven for denne text ligger i layouts/shortcodes/color.html -->
{{<color "grey">}}_The top ThinkCentre is my WFH-computer and not part of the homelab._{{</color>}}

---

## Network
>Gateway/router/firewall: `192.168.1.1`

| Segment | Hostname | IP / Port | Function / Type |
| ---------- | ---------- | -------------------- | -------------- |
| **VLAN 1** | ha.deef.dk | `192.168.1.103:8123` | Home Assistant |
| **VLAN 1** | lv-426.deef.dk | `192.168.1.10:8006` | Proxmox Initial Node |
| **VLAN 1** | lv-223.deef.dk | `192.168.1.11:8006` | Proxmox Node |
| **VLAN 1** | lv-178.deef.dk | `192.168.1.12:8006` | Proxmox Node |
| **VLAN 1** | moode.deef.dk | `192.168.1.248:80` | Moode Audio |
| **VLAN 1** | nginx.deef.dk | `192.168.1.204:81` | Proxy Manager |
| **VLAN 1** | pi-hole.deef.dk | `192.168.1.127:80` | Ad Blocking |
| **VLAN 1** | pi-hole2.deef.dk | `192.168.1.130:80` | Ad Blocking |
| **VLAN 10** | - | `192.168.10.204` | IoT Device |
| **VLAN 10** | - | `192.168.10.104` | IoT Device |
| **VLAN 10** | - | `192.168.10.44` | IoT Device |

---

## Pi-hole

I've installed Pi-hole on two devices. By running two instances, I ensure that ad-blocking and local DNS resolution remain active even if one device goes offline or restarts.

For the Raspberry Pi, install with following commands:
```bash
git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
cd "Pi-hole/automated install/"
sudo bash basic-install.sh
```

For the Proxmox node, install with Proxmox community helper script below.
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/pihole.sh)"
```

Assign `192.168.1.127` and `192.168.1.130` as the primary and secondary DNS servers on your router.

Adding the local DNS records to both pi-holes instances.
![Pi-hole local DNS Records](/images/pi-local-dns.png "Pi-hole local DNS Records")
*Notice the local DNS records all point to the Nginx Proxy Manager IP-address.*

---

## Nginx Proxy Manager

Install with Proxmox community helper script below.
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/nginxproxymanager.sh)"
```

Setting up the DNS records on Cloudflare.

```dns
;Type    Name          Content          Proxy 
A        deef.dk       192.168.1.204    DNS Only - reverse IP
CNAME    *deef.dk      deef.dk          DNS Only
```

Configuring Let's Encrypt SSL Certificate:
![TLS Certificate](/images/create-certificate.png "Create SSL Certificate")

Add your first Proxy Host.
![Add your first Proxy Host](/images/add-proxy-host.png "Add your first Proxy Host")
> Tip:  
> *Sometimes depending on the service you're proxying, you may need to play around with the settings.
> For example, Home Assistant requires you to select `WebSocket Support`. And sometimes the scheme needs to be set to `http` instead of `https`.*

Select the SSL certificate you previously created.
![SSL Certificate Selected](/images/add-proxy-ssl.png "SSL Certificate Selected")

You should now be able to access your services without using the IP-addresses. No more *site not secure* warnings and easy rememberable URLs like `ha.deef.dk`.

Everything is set up to run locally. If you do a nslookup on `ha.deef.dk`, you will only see the local IP-address, not the external IP-address. Even if you're trying to access the service from outside your local network.

Cloudflare acts as a [reverse proxy](https://www.cloudflare.com/learning/cdn/glossary/reverse-proxy/) and handles TLS termination. If you don't have a domain, you can use a free service like DuckDNS.

---

## Home Assistant

HA is installed as a VM on Proxmox with this helper script.

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/haos-vm.sh"
```

For Home Assistant to work with reverse proxy, you need to add the following to the `configuration.yaml` file.

```yaml
# configuration.yaml
http:
  use_x_forwarded_for: true
  trusted_proxies:
    - 192.168.1.204 # <- Your reverse proxy IP here
```

My Home Assistant dashboard fits perfectly on a older iPad I got for cheap.
![Home Assistant Dashboard](/images/ha-dashboard.png "Home Assistant Dashboard")

<!-- Data ligger i assets/snippets/ha-config.yaml, da det fyldte for meget her -->
{{< details title="Click here to see the full HA Dashboard YAML" file="snippets/ha-config.yaml" lang="yaml" >}}{{< /details >}}

---

## Resources
- [Proxmox VE](https://www.proxmox.com/en/)
- [Home Assistant](https://www.home-assistant.io/)
  - [HA Proxmox Helper Script](https://community-scripts.org/scripts/home-assistant?id=home-assistant)
- [Nginx Proxy Manager](https://nginxproxymanager.com/)
  - [NPM Proxmox Helper Script](https://community-scripts.org/scripts/nginxproxymanager?id=nginxproxymanager)
- [Pi-hole](https://pi-hole.net/)
  - [Pi-hole Proxmox Helper Script](https://community-scripts.org/scripts/pihole?id=pihole)

---

## Conclusion

This post got a bit long, but having separated posts about my homelab just didn't make sense, so I decided to put everything in one post. I don't always write into detail, but I hope you find at least some of it useful.
