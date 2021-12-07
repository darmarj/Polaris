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

    Go to **/etc/apt/sources.list.d/pve-enterprise.list** and taka an editor as below:

    ```markdown
    echo "#deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise" 
    > /etc/apt/sources.list.d/pve-enterprise.list
    ```

### Plan A

    * Proxmox --- USTC.edu.cn

    * Debian --- Aliyun

    ```markdown
    wget https://mirrors.ustc.edu.cn/proxmox/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
    echo "deb https://mirrors.ustc.edu.cn/proxmox/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
    echo "deb https://mirrors.ustc.edu.cn/proxmox/debian/ceph-pacific bullseye main" > /etc/apt/sources.list.d/ceph.list
    sed -i.bak "s#http://download.proxmox.com/debian#https://mirrors.ustc.edu.cn/proxmox/debian#g" /usr/share/perl5/PVE/CLI/pveceph.pm
    sed -i.bak "s#ftp.debian.org/debian#mirrors.aliyun.com/debian#g" /etc/apt/sources.list
    sed -i "s#security.debian.org#mirrors.aliyun.com/debian-security#g" /etc/apt/sources.list
    apt update && apt dist-upgrade
    ```

### Plan B

    * Proxmox Chinese Forum

    ```markdown
    wget http://download.proxmox.wiki/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
    echo "deb http://download.proxmox.wiki/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-no-subscription.list
    echo "deb https://download.proxmox.wiki/debian/ceph-pacific bullseye main" > /etc/apt/sources.list.d/ceph.list
    sed -i.bak "s#http://download.proxmox.com/debian#https://download.proxmox.wiki/debian#g" /usr/share/perl5/PVE/CLI/pveceph.pm
    sed -i.bak "s#ftp.debian.org/debian#mirrors.aliyun.com/debian#g" /etc/apt/sources.list
    sed -i "s#security.debian.org#mirrors.aliyun.com/debian-security#g" /etc/apt/sources.list
    apt update && apt dist-upgrade
    ```
