---
title: "Utiliser le portable avec le capot fermé"
publishDate: 2022-07-30
slug: utiliser-le-portable-avec-le-capot-ferme
tags:
  - linux
  - home-server
  - laptop

---

Modifiez le fichier `/etc/systemd/logind.conf` et changez les lignes suivantes:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

Après redémarrez le service:

```shell
sudo systemctl restart systemd-logind.service
```
