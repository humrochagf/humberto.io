---
title: "Usar o laptop com a tela fechada"
publishDate: 2022-07-30
slug: usar-o-laptop-com-a-tela-fechada
tags:
  - linux
  - home-server
  - laptop

---

Edite o arquivo `/etc/systemd/logind.conf` e altere as linhas seguintes:

```ini
HandleLidSwitch=ignore
HandleLidSwitchExternalPower=ignore
HandleLidSwitchDocked=ignore
```

Depois, reinicie o serviço:

```shell
sudo systemctl restart systemd-logind.service
```
