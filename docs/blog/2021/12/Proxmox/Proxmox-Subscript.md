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

    Go to **/usr/share/pve-manager/js/pvemanagerlib.js** and taka an editor:

    __Code__

    ```javascript

        if (res === null || res === undefined || !res ||
        res.data.status.toLowerCase() !== 'active') {

        if (false) {
    ```
