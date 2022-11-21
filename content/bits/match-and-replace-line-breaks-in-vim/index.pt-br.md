---
title: "Busca e substituição de quebras de linha no vim"
publishDate: 2022-02-20
slug: busca-e-substituicao-de-quebras-de-linha-no-vim
tags:
  - vim

---

Existem 2 tipos diferentes de caractere especial que são utilizados para as quebras de linha:

- `\n` representa um caractere de nova linha `0x0a`
- `\r` representa um caractere de retorno de carro `0x0d`

Mas no lado de substituição do padrão de busca e substituição do vim as coisas mudam um pouco:

- `\n` vira um caractere de nulo `0x00`
- `\r` vira um caractere de nova linha `0x0a`

Exemplo:

```text
%s/\n/\r/g
```
