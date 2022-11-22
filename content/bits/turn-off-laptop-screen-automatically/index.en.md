---
title: "Turn off laptop screen automatically"
publishDate: 2022-07-29
tags:
  - linux
  - home-server
  - laptop

---

Edit the `/etc/default/grub` file adding the following to the `GRUB_CMDLINE_LINUX` variable:

```ini
GRUB_CMDLINE_LINUX="consoleblank=300"  # turn off the screen after 5 minutes
```

Then update grub:

```console
sudo update-grub
```
