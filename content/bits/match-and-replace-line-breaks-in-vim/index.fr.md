---
title: "Recherche et remplacement de sauts de ligne dans vim"
publishDate: 2022-02-20
slug: recherche-et-remplacement-de-sauts-de-ligne-dans-vim
tags:
  - vim

---

Il y a deux caractères spéciaux différents qui sont utilisés pour les sauts de ligne:

- `\n` représente le caractère de nouvelle ligne `0x0a`
- `\r` représente le caractère de retour chariot `0x0d`

Mais, dans le coté remplacement du modèle de recherche de vim les choses changent un peut:

- `\n` deviens le caractère nul `0x00`
- `\r` deviens le caractère de retour chariot `0x0a`

Alors, pour rechercher le saut de ligne on utilise le `\n` et pour le remplacer on utilise le `\r`.

Exemple:

```text
%s/\n/\r/g
```
