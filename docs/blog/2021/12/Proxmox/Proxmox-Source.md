---
template: overrides/main.html
description: >
  Proxmox v7 source in PandaWorld
search:
  exclude: true
---

# Proxmox v7 Source in PandaWorld

## Usage

!!! note

    Moving into **/etc/apt/sources.list.d/pve-enterprise.list** and comments the original enterprise sources as below:

```markdown
    echo "#deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise" 
    > /etc/apt/sources.list.d/pve-enterprise.list
```

### Domestic APT resource

    vi /etc/apt/sources.list

```markdown

    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
    deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
    deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
    deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
    deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
    deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
    deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
```
