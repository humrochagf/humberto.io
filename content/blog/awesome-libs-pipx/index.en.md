---
title: "Awesome Libs: pipx"
description: "Discover pipx! An awesome Python library to keep your Python tools safe, updated, and organized"
publishDate: 2019-08-08
tags:
  - awesome-libs
  - python
  - pipx
images:
  - /img/posts/awesome-libs-pipx.png

---

I'm starting this series of posts to give you tips on libraries that can be handy on your day to day as a Developer and also to present libraries I think you should keep on eye on.

One of the perks of a good Developer is having a proper tool-set available on your belt, and nothing more appropriate to start this series than a library that installs other libraries!

How many times did you install some Python CLI tool inside of a Python virtualenv? Have you ever updated some tool and broke another library because it relied on a common dependency?

If you like organization, stability and to not repeat yourself [pipx](https://pipxproject.github.io/pipx/) is an **awesome lib** for you.

![organized kittens image](assets/organized-kittens.jpg)

Pipx is a handy tool to isolate your Python programs into separate environments, exposing their entry points so you can run them from anywhere.

You can also easily upgrade the installed packages without having a conflict between dependencies and have some useful tools like [nox](https://nox.thea.codes/en/stable/), [flake8](http://flake8.pycqa.org/en/latest/), and [black](https://black.readthedocs.io/en/stable/) installed once on your system (no more installing it on every single virtualenv that you create).

To install pipx you have to run:

```console
$ python3 -m pip install --user pipx
$ python3 -m pipx ensurepath
```

The second command is to ensure that you'll have CLI access to run the programs installed with pipx.

Now, you can install your favorite Python tools in isolated environments and have fun \\o/

```console
$ pipx install pyjokes
$ pyjoke
There are two ways to write error-free programs; only the third one works.
```
