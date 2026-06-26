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

I have installed Pi-hole on multiple devices to create a redundant and fault-tolerant network setup. By running multiple instances, I ensure that ad-blocking and DNS resolution remain active even if one device goes offline or restarts.

My current setup consists of two Pi-hole instances across the following hardware:
* **One Proxmox VE node:** Running a virtualized Pi-hole instance.
* **One Raspberry Pi 2 Model B:** Running in headless mode and managed remotely over the network.

I used the following command to install Pi-hole on the Raspberry Pi:
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

<!-- Data ligger i assets/snippets/ha-config.yaml, da det fyldte for meget her -->
{{< details title="Click here to see the full HA Dashboard YAML" file="snippets/ha-config.yaml" lang="yaml" >}}{{< /details >}}

---

## Resources
- [Home Assistant](https://www.home-assistant.io/)
- [Pi-hole](https://pi-hole.net/)
- [Proxmox Home Assistant Script](https://community-scripts.org/scripts/home-assistant?id=home-assistant)
- [Proxmox Pi-hole Script](https://community-scripts.org/scripts/pihole?id=pihole)
- [Proxmox VE](https://www.proxmox.com/en/)
