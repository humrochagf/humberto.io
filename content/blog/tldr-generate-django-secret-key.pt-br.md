---
title: "TLDR: Gerando Secret Key para o Django"
description: Gere a Secret Key do Django direto do terminal sem precisar recorrer a um site na internet
publishDate: 2019-07-12
images:
  - /img/tldr/secret-key.png
slug: tldr-gerando-secret-key-para-o-django
tags: ["tldr", "python", "django"]
aliases: [
  "/blog/tldr-gerando-secret-key-para-o-django"
]
draft: false

---

Levante a mão quem nunca versionou a `SECRET_KEY` do Django no início de um projeto e precisou gerar uma nova na hora de subir pra produção.

Este **TLDR** é um lembrete rápido de como você pode regerar uma secret key localmente, sem recorrer a sites na internet para gera-la para você.

Como o Django gera a secret key no início de um projeto, já existe esta função implementada em seu código e você pode acessá-la desta forma:

{{< highlight python >}}
from django.core.management.utils import get_random_secret_key

print(get_random_secret_key())
{{< / highlight >}}

Se não quiser nem ter o trabalho de abrir o shell do Python você pode executar direto do terminal:

{{< highlight console >}}
python -c 'from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())'
{{< / highlight >}}

{{% tip class="info" %}}
Não se esqueça que é preciso ter o Django instalado no ambiente em que você for rodar o comando.
{{% /tip %}}
