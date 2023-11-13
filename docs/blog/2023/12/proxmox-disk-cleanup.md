---
template: overrides/main.html
description: >
  Proxmox
search:
  exclude: true
---

# Proxmox disk cleanup

```shell
# use ncdu command to check the directory usage under root
nudc /
```

# Linux old kernel cleanup
```shell
OLD_KERNEL= dpkg -l | awk '{print $2}' | grep -e ^pve-.*$c

apt-get remove --purge $OLD_KERNEL
```
