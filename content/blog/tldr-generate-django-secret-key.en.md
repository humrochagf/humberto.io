---
title: "TLDR: Generate Django Secret Key"
description: Generate Django's secret key right from your terminal without needing to risk yourself on any website
publishDate: 2019-07-12
images:
  - /img/tldr/secret-key.png
tags: ["tldr", "python", "django"]
draft: false

---

Raise your hand if you never versioned the Django's `SECRET_KEY` at the beginning of a project and needed to generate a new one before going to production.

This **TLDR** is a quick reminder of how to generate a secret key locally, without going to some website on the internet to generate it for you.

Django generates a secret key every time that you create a new project, sou this function already exists at its code, and you can access it in this way:

{{< highlight python >}}
from django.core.management.utils import get_random_secret_key

print(get_random_secret_key())
{{< / highlight >}}

If you don't even want to have the work of stating the Python shell, you can execute this command on the terminal:

{{< highlight console >}}
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
{{< / highlight >}}

{{< tip class="info" >}}
Remember that you need Django installed at the environment to run this command.
{{< /tip >}}
