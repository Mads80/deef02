+++
date = '2026-06-26T12:56:50+02:00'
draft = true
title = 'Homelabbing'
+++

Hardware
- 3× Lenovo ThinkCentre M920x 16 GB RAM, 256 GB NVMe
- Raspberry Pi 3B with HiFiBerry AMP+
- Raspberry Pi 2B
Network
- Cloud Gateway Ultra
- USW Flex Mini
- U7 Lite AP

![My Homelab](/images/IMG_7091.jpg)
{{<color "grey">}}*The top computer is my workcomputer and not part of the homelab.*{{</color>}}

---

### Network and VLANs

| Segment | Enhed / SSID | IP-adresse | Funktion / Type |
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

| Option | Description |
| ------ | ----------- |
| data   | path to data files to supply the data that will be passed into templates. |
| engine | engine to be used for processing templates. Handlebars is the default. |
| ext    | extension to be used for dest files. |
