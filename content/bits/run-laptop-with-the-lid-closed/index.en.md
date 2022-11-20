---
title: "Run laptop with the lid closed"
publishDate: 2022-07-30
tags:
  - linux
  - home-server
  - laptop

---

Edit the `/etc/systemd/logind.conf` file, and change the following lines:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

Then restart the service:

```shell
sudo systemctl restart systemd-logind.service
```
