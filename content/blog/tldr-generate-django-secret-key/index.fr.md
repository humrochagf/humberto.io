---
title: "TLDR: Comment créer une Django Secret Key"
description: "Comment créer une nouvelle Secret Key dans la ligne de commande pour l'utiliser avec Django sans y recourir à un site web"
publishDate: 2019-07-12
slug: tldr-comment-creer-une-django-secret-key
tags:
  - tldr
  - python
  - django
images:
  - /img/posts/tldr-generate-django-secret-key.png
aliases:
  - tldr-generate-django-secret-key

---

Enlevez la main qui n'a jamais ajouté une `SECRET_KEY` Django dans un commit au debout d'un projet e après, vous avez besoin de créer une nouvelle clé avant de l'envoyer à la prod?

Ce **TLDR** est un rappel rapide de comment créer une clé secrète, sans utiliser un site web pour le faire.

Django crée une clé toujours quand on commence un nouveau projet, donc cette fonction est déjà là et on va l'utiliser de cette façon:

{{< highlight python >}}
from django.core.management.utils import get_random_secret_key

print(get_random_secret_key())
{{< / highlight >}}

Si on ne veut pas ouvrir une session Python, il est possible de l'exécuter directement avec la ligne de commande:

{{< highlight console >}}
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
{{< / highlight >}}

{{< tip class="info" >}}
N'oubliez pas qu'il faut avoir installé Django dans votre environnement après exécuter l'opération.
{{< /tip >}}

## Actualisation Python 3.6+

Grâce à interaction de [@ChristianHeimes](https://twitter.com/ChristianHeimes) et [@pauloxnet](https://twitter.com/pauloxnet) dans un postage de [@aclark4life](https://twitter.com/aclark4life) chez twitter. J'ai appris que à partir de la version Python 3.6 la bibliothèque [secrets](https://docs.python.org/3/library/secrets.html) a été ajoutée pour qui on puisse l'utiliser dans la création et manutention des chiffres aléatoires comme des clés secrètes.

C'est une bonne solution qu'utilise le concept de **piles fournies** de Python et on peut l'utiliser pour créer la `SECRET_KEY` sans avoir Django installé:

{{< highlight python >}}
import secrets

print(secrets.token_urlsafe())
{{< / highlight >}}

Vous pouvez aussi utiliser la ligne de commande:

{{< highlight console >}}
python -c "import secrets; print(secrets.token_urlsafe())"
{{< / highlight >}}

Et, si vous vous êtes déjà demandé comment Django fait ça désormais? Bon, ils l'utilisent aussi. 🎉

En commençant par la version 3.1.3 la fonction `get_random_secret_key` marche comme ça:

{{< highlight python >}}
import secrets

length = 50
chars = 'abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*(-_=+)'

secret_key = ''.join(secrets.choice(chars) for i in range(length))

print(secret_key)
{{< / highlight >}}
