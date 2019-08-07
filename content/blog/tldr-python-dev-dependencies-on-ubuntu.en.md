---
title: "TLDR: Python Dev Dependencies on Ubuntu"
description: Discover the external dependencies to use at your Python development environment  on Ubuntu
publishDate: 2018-06-19
images:
  - /img/generics/python-ubuntu.jpg
tags: ["tldr", "python", "ubuntu"]
draft: false

---

Some libraries aren't 100% packaged Python code, some of them are **bindings** to external libraries. They offer a Python interface so you can call them inside your code. On these cases, we need to install them previously at the system so the python package can work.

When that happens, we get this kind of error:

{{< highlight console >}}
ImproperlyConfigured: Error loading either pysqlite2 or sqlite3 modules (tried in that order): No module named _sqlite3
{{< / highlight >}}

To solve errors like that, you need to install the external library, and the system configures everything to you. However, if you work with multiple Python versions managed by [pyenv](https://github.com/pyenv/pyenv), you have to rebuild all python versions every time that you remember to install a new dependency.

{{< videogif "/img/memes/here-we-go-woodpecker-witch.webm" >}}

To avoid suffering from that, after installing the OS, and before mounting your Python environment, I like to install all the main external dependencies that I'm used to using:

{{< highlight console >}}
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
{{< / highlight >}}

{{% tip class="info" %}}
This command was tested at **Ubuntu 18.04**, the names of the packages can change from one version to another.
{{% /tip %}}

## Bonus

For everyone that wants to know the use cases of each dependency, I made a breakdown describing them:

{{< highlight console >}}
$ sudo apt-get install dpkg-dev build-essential
{{< / highlight >}}

Installs the C and C++ compilers, also the make command used to compile the CPython dependencies.

{{< highlight console >}}
$ sudo apt-get install python-dev python3-dev
{{< / highlight >}}

Installs the headers and static libraries of python 2 and 3 that are used to compile and mount the language extensions.

{{< highlight console >}}
$ sudo apt-get install libncursesw5-dev
{{< / highlight >}}

Library used on packages that deal with the terminal interface, like [visidata](https://github.com/saulpw/visidata) for example.

{{< highlight console >}}
$ sudo apt-get install libsqlite3-dev libreadline-dev libbz2-dev
{{< / highlight >}}

Libraries used to work with [SQLite](https://www.sqlite.org/index.html) databases.

{{< highlight console >}}
$ sudo apt-get install libffi-dev libssl-dev
{{< / highlight >}}

Libraries used on packages which implement **OpenSSL**.

{{< highlight console >}}
$ sudo apt-get install libgdbm-dev
{{< / highlight >}}

The base of the most of packages that handle **databases**.

{{< highlight console >}}
$ sudo apt-get install zlib1g-dev libjpeg-dev libtiff-dev
{{< / highlight >}}

These are external dependencies of packages used for image processing like [Pillow](https://github.com/python-pillow/Pillow).

{{< highlight console >}}
$ sudo apt-get install libpq-dev
{{< / highlight >}}

It is the dependency of [psycopg2](https://github.com/psycopg/psycopg2) to work with **PostgreSQL** databases.

{{< highlight console >}}
$ sudo apt-get install libxml2-dev libxslt1-dev
{{< / highlight >}}

Libraries used to handle XML on packages like [lxml](https://github.com/lxml/lxml).

{{< highlight console >}}
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
{{< / highlight >}}

This group is used to install packages that work with window management and graphical interfaces like [wxPhoenix](https://github.com/wxWidgets/Phoenix). On a previous post, I taught [how to organize a coding dojo](/en/blog/coding-dojo-101/), and I told about a TDD semaphore called [dose](https://github.com/danilobellini/dose) that uses wxPhoenix to run at Python 3 environments.

Installing these dependencies you'll be able to install it without problems.
