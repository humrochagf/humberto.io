---
title: "Desligar automaticamente a tela do laptop"
publishDate: 2022-07-29
slug: desligar-automaticamente-a-tela-do-laptop
tags:
  - linux
  - home-server
  - laptop

---

Modifique o arquivo `/etc/default/grub` e adicione o seguinte conteúdo na variável `GRUB_CMDLINE_LINUX`:

```ini
GRUB_CMDLINE_LINUX="consoleblank=300"  # desliga a tela após 5 minutos
```

Em seguida atualize o grub:

```shell
sudo update-grub
```
