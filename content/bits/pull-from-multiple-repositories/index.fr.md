---
title: "Mise a jour de plusieurs dépôts"
publishDate: 2022-05-23
slug: mise-a-jour-de-plusieurs-depots
tags:
  - git

---

Une astuce pour quand on a une liste de dépôts git à mettre a jour:

```console
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && git pull && cd .." \;
```

La partie `\( ! -name . \)` de la commande est utilisée pour n'est pas exécuter la commande avec le fichier `.`.
