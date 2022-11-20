---
title: "TLDR: Dependências Dev Python no Ubuntu"
description: "Configure as principais dependencias externas de um ambiente de desenvolvimento python no Ubuntu"
publishDate: 2018-06-19
slug: tldr-dependencias-dev-python-no-ubuntu
tags:
  - tldr
  - python
  - ubuntu
images:
  - /img/posts/tldr-python-dev-dependencies-on-ubuntu.jpg
aliases:
  - tldr-python-dev-dependencies-on-ubuntu
  - /blog/tldr-dependencias-dev-python-no-ubuntu

---

Algumas bibliotecas do python como a que usamos para conversar um banco **SQLite** são na prática um **encapsulamento de um binário externo**. Nestes casos precisamos que eles estejam instalados em nosso sistema operacional para que determinado pacote python funcione.

Quando isso acontece, recebemos erros como este:

```console
ImproperlyConfigured: Error loading either pysqlite2 or sqlite3 modules (tried in that order): No module named _sqlite3
```

Para resolver estes problemas basta instalar esta dependência externa que normalmente o sistema já reconfigura tudo para você, mas para quem trabalha múltiplas versões do python e com [pyenv](https://github.com/pyenv/pyenv) é preciso recompilar todas as versões do python toda vez que lembra de uma dependência externa.

{{< videogif "assets/here-we-go-woodpecker-witch.webm" >}}

Para não sofrer com este problema, após instalar o sistema operacional e antes de preparar o ambiente de desenvolvimento eu já instalo as principais dependências que costumo utilizar:

```console
$ sudo apt-get install dpkg-dev \
                       build-essential \
                       python-dev \
                       python3-dev \
                       libncursesw5-dev \
                       libsqlite3-dev \
                       libreadline-dev \
                       libbz2-dev \
                       libffi-dev \
                       libssl-dev \
                       libgdbm-dev \
                       zlib1g-dev \
                       libjpeg-dev \
                       libtiff-dev \
                       libpq-dev \
                       libxml2-dev \
                       libxslt1-dev \
                       libsdl2-dev \
                       libgstreamer-plugins-base1.0-dev \
                       libnotify-dev \
                       freeglut3-dev \
                       libsm-dev \
                       libgtk-3-dev \
                       libwebkitgtk-3.0-dev \
                       libxtst-dev
```

{{< tip class="info" >}}
Este comando foi testado no **Ubuntu 18.04**, os nomes dos pacotes podem mudar de uma versão para outra.
{{< /tip >}}

## Bônus

Para quem tiver curiosidade ou quer instalar somente alguns dos itens da lista quebrei o comando acima descrevendo cada um:

```console
$ sudo apt-get install dpkg-dev build-essential
```

Instala os compiladores de C e C++ bem como o comando make essenciais para compilar as dependências em cpython.

```console
$ sudo apt-get install python-dev python3-dev
```

Instala os headers e bibliotecas estáticas do python 2 e 3 que são usadas para montar extensões  compiladas da linguagem.

```console
$ sudo apt-get install libncursesw5-dev
```

Biblioteca usada em pacotes que lidam com interface de terminal como por exemplo o [visidata](https://github.com/saulpw/visidata).

```console
$ sudo apt-get install libsqlite3-dev libreadline-dev libbz2-dev
```

Bibliotecas usadas para trabalhar com bancos de dados [SQLite](https://www.sqlite.org/index.html).

```console
$ sudo apt-get install libffi-dev libssl-dev
```

Bibliotecas usadas em pacotes que implementam **OpenSSL**.

```console
$ sudo apt-get install libgdbm-dev
```

Base para boa parte dos pacotes que lidam com **banco de dados**.

```console
$ sudo apt-get install zlib1g-dev libjpeg-dev libtiff-dev
```

Dependências externas de pacotes que tratam imagem como o [Pillow](https://github.com/python-pillow/Pillow)

```console
$ sudo apt-get install libpq-dev
```

Dependência do [psycopg2](https://github.com/psycopg/psycopg2) para lidar com bancos **PostgreSQL**.

```console
$ sudo apt-get install libxml2-dev libxslt1-dev
```

Bibliotecas usadas para tratar xml como o pacote [lxml](https://github.com/lxml/lxml)

```console
$ sudo apt-get install dpkg-dev \
                       build-essential \
                       libjpeg-dev \
                       libtiff-dev \
                       libsdl2-dev \
                       libgstreamer-plugins-base1.0-dev \
                       libnotify-dev \
                       freeglut3-dev \
                       libsm-dev \
                       libgtk-3-dev \
                       libwebkitgtk-3.0-dev \
                       libxtst-dev
```

Este conjunto em específico é utilizado para instalar pacotes de gerenciamento de janelas em interface gráfica como o [wxPhoenix](https://github.com/wxWidgets/Phoenix). Em uma postagem anterior em que eu falei sobre [como organizar um dojo](/pt-br/blog/coding-dojo-101/) foi citado um semáforo para TDD chamado [dose](https://github.com/danilobellini/dose) que depende do wxPhoenix para rodar em ambientes python 3.

Instalando estas dependências é possível realizar sua instalação sem problemas.
