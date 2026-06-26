+++
date = '2026-06-25T17:52:28+02:00'
draft = true
title = 'Pi-hole'
tags = ["pi-hole", "ad-blocking", "dns", "network"]
+++

I have Pi-hole installed on more than one device. The reason for this is that with more than one device, I can a more robust setup. For example, I have it installed on my raspberry pi and my Proxmox VE, so that if one device goes down, the other can still block ads.

Right now Pi-hole is running on my Proxmox VE in two different nodes and a Raspberry Pi Model 2B.

For the Raspberry Pi Model 2B, I have it running in headless mode, so that it can be controlled remotely. I used the code below to install it.

```bash
git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
cd "Pi-hole/automated install/"
sudo bash basic-install.sh
```

For the Proxmox VE nodes, I used the community script below to install it.
```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/ct/pihole.sh)"
```

Resources:
- [Pi-hole](https://pi-hole.net/)
- [Proxmox VE](https://www.proxmox.com/en/)
- [Proxmox VE Pi-hole Script](https://community-scripts.org/scripts/pihole?id=pihole)
