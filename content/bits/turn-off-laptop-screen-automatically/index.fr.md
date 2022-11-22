---
title: "Éteindre automatiquement l'écran du portable"
publishDate: 2022-07-29
slug: eteindre-automatiquement-l-ecran-du-portable
tags:
  - linux
  - home-server
  - laptop

---

Modifiez le fichier `/etc/default/grub` et ajoutez le contenu suivant à la variable `GRUB_CMDLINE_LINUX`:

```ini
GRUB_CMDLINE_LINUX="consoleblank=300"  # éteindre l'écran après 5 minutes
```

Puis mettez a jour grub:

```console
sudo update-grub
```
