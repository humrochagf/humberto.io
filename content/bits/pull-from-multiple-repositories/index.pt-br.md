---
title: "Atualizar múltiplos repositórios"
publishDate: 2022-05-23
slug: atualizar-multiplos-repositorios
tags:
  - git

---

Uma dica para quando temos uma lista de repositórios para atualizar:

```shell
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && git pull && cd .." \;
```

A parte `\( ! -name . \)` do comando é usada para evitar de executar o comando na pasta `.`.
