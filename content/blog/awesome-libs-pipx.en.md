+++
draft = true
title = "Awesome Libs: pipx"
description: Make your kick start at pygame, learn how to install, use and write your first program with the pygame library
publishDate: 2019-08-08
images:
  - /img/awesome-libs/pipx.png
tags: ["python", "pipx", "awesome-libs"]

+++
One of the main perks of a good Developer is having a proper tool-set available on your belt.

I'm starting this series of posts to give you tips on libraries that can be handy on your day to day as a Developer and also to present libraries I think you should keep on eye on.

So, nothing more appropriate to start than a library that installs other libraries :)

So our first lib is handy for you that loose count how many times you installed some python tool into a Python virtual environment.

Pipx is a handy tool to isolate your Python programs into separate environments, exposing their entry points so you can run from anywhere.

Other positive points that worth to mention is that you can easily upgrade the installed packages without having a conflict between dependencies, and also have some useful tools like \[nox\](), \[flake8\]() and \[black\]() installed once on your system (no more installing it on every single virtualenv that you create).

{{< highlight python >}}
python3 -m pip install --user pipx
{{< / highlight >}}

{{< highlight python >}}
python3 -m pipx ensurepath
{{< / highlight >}}
