---
template: overrides/main.html
description: >
  OpenWRT
search:
  exclude: true
---

# OpenWRT
## Parition switch

```shell
root@lede:~# fw_printenv boot_part
boot_part=1
```

```shell
fw_setenv boot_part 2
reboot
```
