---
template: overrides/main.html
description: >
  Transparent Gnome
search:
  exclude: true
---

# OpenWRT parition switch for ROM flash

```shell
root@lede:~# fw_printenv boot_part
boot_part=1
```

```shell
fw_setenv boot_part 2
reboot
```
