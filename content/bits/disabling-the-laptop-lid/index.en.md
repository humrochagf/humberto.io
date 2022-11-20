---
title: "Disabling the Laptop Lid"
publishDate: 2022-07-30
tags:
  - linux
  - home-server
  - laptop

---

Edit the `/etc/systemd/logind.conf` file, by uncommenting the following lines and setting them to ignore:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

Then restart the service:

```shell
sudo systemctl restart systemd-logind.service
```

This will disable the laptop lid actions of suspending and turning on/off when closing and opening it.
