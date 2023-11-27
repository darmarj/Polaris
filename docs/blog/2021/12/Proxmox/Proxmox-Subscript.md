---
template: overrides/main.html
description: >
  Proxmox v7 Subscription disable
search:
  exclude: true
---

# Proxmox v7 Subscription

## Usage

!!! note

    Move into **/usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js** and chage as following:

__Code__

```javascript

    if (res === null || res === undefined ||!res || res.data.status.toLowerCase() !== 'active') {

    if (false) {
```
```bash

    systemctl restart pveproxy.service
    reboot
```
