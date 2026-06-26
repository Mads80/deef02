+++
date = '2026-06-26T12:56:50+02:00'
draft = true
title = 'Homelabbing'
tags = ['homelab', 'proxmox', 'raspberry pi', 'home assistant', 'pi-hole']
+++

## Hardware
- 3× Lenovo ThinkCentre M920x 16 GB RAM, 256 GB NVMe
- Raspberry Pi 3B with HiFiBerry AMP+
- Raspberry Pi 2B
- Cloud Gateway Ultra
- USW Flex Mini
- U7 Lite AP


![My Homelab](/images/IMG_7091.jpg)
{{<color "grey">}}_The top ThinkCentre is my workcomputer and not part of the homelab._{{</color>}}

---

## Network

| Segment | Name | IP | Function / Type |
| :--- | :--- | :--- | :--- |
| **VLAN 1** | lv-426 Proxmox | `192.168.1.10` | Proxmox Node |
| **VLAN 1** | lv-223 Proxmox | `192.168.1.11` | Proxmox Node |
| **VLAN 1** | lv-178 Proxmox | `192.168.1.12` | Proxmox Node |
| **VLAN 1** | Pi-hole Primary | `192.168.1.127` | DNS / Blocking |
| **VLAN 1** | Pi-hole Secondary | `192.168.1.130` | DNS / Backup |
| **VLAN 1** | Home Assistant | `192.168.1.103` | Smart Home Controller |
| **VLAN 10** | Atomi wlan0 | `192.168.10.204` | IoT Device |
| **VLAN 10** | Atomi wlan0 | `192.168.10.104` | IoT Device |
| **VLAN 10** | Atomi wlan0 | `192.168.10.44` | IoT Device |

---

## Pi-hole

I have Pi-hole installed on more than one device. The reason for this is that with more than one device, I can a more robust setup. For example, I have it installed on my raspberry pi and my Proxmox VE, so that if one device goes down, the other can still block ads.

Right now Pi-hole is running on my Proxmox VE in two different nodes and a Raspberry Pi Model 2B.

For the Raspberry Pi Model 2B, I have it running in headless mode, so that it can be controlled remotely. I used the code below to install.

```bash
git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
cd "Pi-hole/automated install/"
sudo bash basic-install.sh
```

For the Proxmox VE nodes, I used the community script below to install.
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/pihole.sh)"
```

---

## Home Assistant

HA is installed as a VM on Proxmox with this script.

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/vm/haos-vm.sh"
```

My Home Assistant dashboard.
![Home Assistant Dashboard](/images/IMG_0006.PNG)

{{< details title="Click here to see the full HA Dashboard YAML" file="snippets/ha-config.yaml" lang="yaml" >}}{{< /details >}}

---

## Resources
- [Home Assistant](https://www.home-assistant.io/)
- [Pi-hole](https://pi-hole.net/)
- [Proxmox Home Assistant Script](https://community-scripts.org/scripts/home-assistant?id=home-assistant)
- [Proxmox Pi-hole Script](https://community-scripts.org/scripts/pihole?id=pihole)
- [Proxmox VE](https://www.proxmox.com/en/)
