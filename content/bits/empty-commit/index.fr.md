---
title: "Commit vide"
publishDate: 2022-02-18
slug: commit-vide
tags:
  - git

---

Parfois on veut juste faire un commit vide pour démarrer le CI, ou pour démarrer un webhook.

```console
git commit --allow-empty -m "Trigger CI"
```
