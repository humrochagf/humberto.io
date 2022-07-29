---
title: "Les Bibliothèques Fantastiques: pipx"
description: "Découvrez pipx! Une bibliothèque Python qui nous aide à maintenir des outils organisés, actualisés et sécurises"
publishDate: 2019-08-08
slug: les-bibliotheques-fantastiques-pipx
tags:
  - awesome-libs
  - python
  - pipx
images:
  - /img/posts/awesome-libs-pipx.png
aliases:
  - awesome-libs-pipx

---

Je commence cette série d'articles pour vous donner des conseils sur les bibliothèques qui peuvent vous être utiles au quotidien en tant que développeur et aussi pour présenter des bibliothèques que je pense que vous devriez surveiller.

L'un des avantages d'un bon développeur est d'avoir un ensemble d'outils appropriés disponibles dans votre "ceinture utilitaire", et rien de plus approprié pour commencer cette série qu'une bibliothèque qui installe d'autres bibliothèques!

Combien de fois avez-vous installé les mêmes groupes de Python CLI dans un virtualenv Python? Avez-vous déjà mis à jour un outil et cassé une autre bibliothèque parce qu'elle partageait une dépendance en commun?

Si vous aimez être organisé, avoir plus de stabilité et de ne pas vous répéter [pipx](https://pipxproject.github.io/pipx/) est une **bibliothèque géniale** pour vous.

![organized kittens image](assets/organized-kittens.jpg)

Pipx est un outil pratique pour isoler vos programmes Python dans des environnements séparés, exposant leurs points d'entrée afin que vous puissiez les exécuter de n'importe où.

Vous pouvez aussi mettre à niveau les paquets installés sans conflit entre les dépendances et disposer des outils comme [nox](https://nox.thea.codes/en/stable/), [flake8](http://flake8. pycqa.org/en/latest/) et [black](https://black.readthedocs.io/en/stable/) installés une fois sur votre système (plus besoin de les installer sur chaque virtualenv que vous créez).

Pour installer pipx vous pouvez exécuter la commande:

{{< highlight console >}}
$ python3 -m pip install --user pipx
$ python3 -m pipx ensurepath
{{< / highlight >}}

La deuxième commande est pour vous assurer que le chemin d'utilisation des programmes est disponible avec pipx.

Désormais, vous pouvez installer votre outils Python préférés dans les environnements isolés \\o/

{{< highlight lang >}}
$ pipx install pyjokes
$ pyjoke
There are two ways to write error-free programs; only the third one works.
{{< / highlight >}}
