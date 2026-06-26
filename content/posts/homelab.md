+++
date = '2026-06-26T12:56:50+02:00'
draft = true
title = 'Homelabbing'
tags = ['homelab', 'proxmox', 'raspberry pi', 'home assistant']
+++

### Hardware
- 3× Lenovo ThinkCentre M920x 16 GB RAM, 256 GB NVMe
- Raspberry Pi 3B with HiFiBerry AMP+
- Raspberry Pi 2B
- Cloud Gateway Ultra
- USW Flex Mini
- U7 Lite AP


![My Homelab](/images/IMG_7091.jpg)
{{<color "grey">}}_The top ThinkCentre is my workcomputer and not part of the homelab._{{</color>}}

---

### Network

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

### Pi-hole

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

Resources:
- [Pi-hole](https://pi-hole.net/)
- [Proxmox VE](https://www.proxmox.com/en/)
- [Proxmox VE Pi-hole Script](https://community-scripts.org/scripts/pihole?id=pihole)

---
### Home Assistant

My Home Assistant dashboard.
![Home Assistant Dashboard](/images/IMG_0006.PNG)
{{<details "Click here to see the full HA Dashboard YAML">}}
```yaml
type: sections
icon: mdi:button-pointer
cards: []
badges: []
subview: false
show_icon_and_title: true
max_columns: 2
sections:
  - type: grid
    cards:
      - square: true
        type: grid
        cards:
          - show_name: true
            show_icon: true
            type: button
            entity: switch.moode_socket_1
            show_state: true
            color: green
            name: HELL
            icon: mdi:monitor
          - show_name: true
            show_icon: true
            type: button
            entity: switch.moode_player_socket_1
            show_state: true
            color: green
            name: moOde
            icon: mdi:music
        columns: 2
        grid_options:
          rows: 4
          columns: 12
      - type: heading
        icon: mdi:server-network
        badges:
          - type: entity
            show_state: true
            show_icon: true
            entity: sensor.cpu_total_proxmox
            state_content: state
            icon: mdi:cpu-64-bit
            color: white
          - type: entity
            show_state: true
            show_icon: true
            entity: sensor.ram_total
            icon: mdi:memory
            state_content: state
            color: white
          - type: entity
            show_state: true
            show_icon: true
            entity: switch.proxmox_socket_1
            color: green
          - type: entity
            show_state: true
            show_icon: false
            entity: sensor.cluster_uptime
        heading_style: title
        grid_options:
          rows: auto
          columns: full
      - type: statistics-graph
        entities:
          - entity: sensor.192_168_1_10_package_id_0_temperature
            name: lv-426
            color: deep-orange
          - entity: sensor.192_168_1_11_package_id_0_temperature
            name: lv-223
            color: red
          - entity: sensor.192_168_1_12_package_id_0_temperature
            name: lv-178
            color: accent
        days_to_show: 5
        period: day
        chart_type: bar
        stat_types:
          - mean
        hide_legend: false
        expand_legend: false
        logarithmic_scale: false
        energy_date_selection: false
      - square: false
        type: grid
        columns: 3
        cards:
          - type: tile
            entity: sensor.192_168_1_10_package_id_0_temperature
            name: lv-426
            icon: mdi:thermometer
            color: deep-orange
            show_entity_picture: false
            hide_state: false
            vertical: false
            features:
              - type: trend-graph
                hours_to_show: 24
                detail: false
            features_position: bottom
          - type: tile
            entity: sensor.192_168_1_11_package_id_0_temperature
            name: lv-223
            icon: mdi:thermometer
            color: red
            show_entity_picture: false
            hide_state: false
            vertical: false
            features:
              - type: trend-graph
                hours_to_show: 24
                detail: false
            features_position: bottom
          - type: tile
            entity: sensor.192_168_1_12_package_id_0_temperature
            name: lv-178
            icon: mdi:thermometer
            color: accent
            show_entity_picture: false
            hide_state: false
            vertical: false
            features:
              - type: trend-graph
                hours_to_show: 24
                detail: false
            features_position: bottom
  - type: grid
    cards:
      - show_current: true
        show_forecast: true
        type: weather-forecast
        entity: weather.forecast_home
        forecast_type: hourly
        forecast_slots: 7
        round_temperature: true
        grid_options:
          columns: 12
          rows: 4
        secondary_info_attribute: wind_speed
      - type: heading
        icon: mdi:flash
        heading_style: title
        badges:
          - type: entity
            show_state: true
            show_icon: true
            entity: sensor.proxmox_total_energy
            color: purple
          - type: entity
            show_state: true
            show_icon: true
            entity: sensor.moode_player_total_energy
            color: green
          - type: entity
            show_state: true
            show_icon: true
            entity: sensor.moode_total_energy
            color: primary
          - type: entity
            show_state: true
            show_icon: true
            entity: sensor.total_power_usage
            state_content: state
            name: Total
            icon: ""
      - type: statistics-graph
        entities:
          - entity: sensor.proxmox_total_energy
            name: Proxmox Cluster
            color: purple
          - entity: sensor.moode_player_total_energy
            name: moOde Player
            color: green
          - entity: sensor.moode_total_energy
            name: HELL
            color: cyan
        days_to_show: 5
        period: day
        chart_type: bar
        stat_types:
          - change
        hide_legend: false
        expand_legend: false
        logarithmic_scale: false
        energy_date_selection: false
      - square: false
        type: grid
        columns: 3
        cards:
          - type: tile
            entity: sensor.proxmox_power
            name: Proxmox
            icon: mdi:thermometer
            color: purple
            show_entity_picture: false
            hide_state: false
            vertical: false
            features:
              - type: trend-graph
                hours_to_show: 24
                detail: false
            features_position: bottom
          - type: tile
            entity: sensor.moode_player_power
            name: moOde
            icon: mdi:thermometer
            color: green
            show_entity_picture: false
            hide_state: false
            vertical: false
            features:
              - type: trend-graph
                hours_to_show: 24
                detail: false
            features_position: bottom
          - type: tile
            entity: sensor.moode_power
            name: HELL
            icon: mdi:thermometer
            color: cyan
            show_entity_picture: false
            hide_state: false
            vertical: false
            features:
              - type: trend-graph
                hours_to_show: 24
                detail: false
            features_position: bottom
dense_section_placement: false
footer: {}
```
{{</details>}}
