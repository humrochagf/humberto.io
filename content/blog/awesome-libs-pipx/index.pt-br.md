---
title: "Libs Fantásticas: pipx"
description: "Conheça o pipx! Uma lib fantástica para manter seus pacotes Python seguros, atualizados e organizados"
publishDate: 2019-08-08
slug: libs-fantasticas-pipx
tags:
  - awesome-libs
  - python
  - pipx
images:
  - /img/posts/awesome-libs-pipx.png
aliases:
  - awesome-libs-pipx
  - libs-fantasticas

---

Estou começando esta série para dar dicas sobre bibliotecas que podem ser muito úteis no seu dia a dia, e também para apresentar bibliotecas interessantes nas quais que você deveria ficar de olho.

Uma das habilidades de um bom Programador é ter a ferramenta certa para realizar seu trabalho, e nada mais apropriado que começar esta série com uma ferramenta que instala outras ferramentas!

Quantas vezes você já teve que instalar algum programa Python em uma virtualenv que você acabou de criar? Já passou pela frustração de atualizar uma biblioteca e quebrar sua árvore de dependências?

Se você gosta de manter as coisas organizadas, gosta de estabilidade e de não ter que ficar se repetindo toda hora [pipx](https://pipxproject.github.io/pipx/) é uma **lib fantástica** para você!

![organized kittens image](assets/organized-kittens.jpg)

Pipx é uma ferramenta muito útil que serve para instalar programas Python em ambientes isolados expondo suas interfaces de linha de comando para que você possa executá-los de qualquer lugar.

Com isso você pode manter tudo atualizado com facilidade sem ter conflitos entre dependências, além de manter somente uma instalação de cada ferramenta como por exemplo [nox](https://nox.thea.codes/en/stable/), [flake8](http://flake8.pycqa.org/en/latest/), e [black](https://black.readthedocs.io/en/stable/) (nada de ficar instalando novamente em todo virtualenv que você cria).

Para instalar o pipx você precisa rodar os seguintes comandos:

```console
$ python3 -m pip install --user pipx
$ python3 -m pipx ensurepath
```

O segundo comando garante que o caminho dos executáveis estejam disponíveis para acesso via linha de comando.

Agora você pode instalar sua ferramenta favorita em um ambiente exclusivo e se divertir \\o/

```console
$ pipx install pyjokes
$ pyjoke
There are two ways to write error-free programs; only the third one works.
```
