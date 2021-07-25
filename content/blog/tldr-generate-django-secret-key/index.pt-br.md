---
title: "TLDR: Gerando Secret Key para o Django"
description: "Gere a Secret Key do Django direto do terminal sem precisar recorrer a um site na internet"
publishDate: 2019-07-12
slug: tldr-gerando-secret-key-para-o-django
tags:
  - tldr
  - python
  - django
images:
  - /img/posts/tldr-generate-django-secret-key.png
aliases:
  - tldr-generate-django-secret-key
  - /blog/tldr-gerando-secret-key-para-o-django

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

{{< tip class="info" >}}
Não se esqueça que é preciso ter o Django instalado no ambiente em que você for rodar o comando.
{{< /tip >}}

## Atualização para Python 3.6+

Graças a interação de [@ChristianHeimes](https://twitter.com/ChristianHeimes) e [@pauloxnet](https://twitter.com/pauloxnet) na postagem do [@aclark4life](https://twitter.com/aclark4life) no twitter. Eu aprendi que a partir da versão 3.6 to Python a biblioteca [secrets](https://docs.python.org/3/library/secrets.html) foi adicionada para ajudar com a geração de números aleatórios criptograficamente fortes para serem usados para gerenciamento de dados como senhas, autenticação de contas tokens de segurança entre outros.

É uma solução legal e com **baterias inclusas** pra gerar sua `SECRET_KEY` sem ter o Django como dependência:

{{< highlight python >}}
import secrets

print(secrets.token_urlsafe())
{{< / highlight >}}

Você também pode usar pelo terminal:

{{< highlight console >}}
python -c "import secrets; print(secrets.token_urlsafe())"
{{< / highlight >}}

E caso tenha ficado curioso sobre como o Django faz isso hoje em dia, adivinha? Eles também usam esta solução. 🎉

Começando da versão de tag 3.1.3 isso é o que a função `get_random_secret_key` faz por trás dos panos:

{{< highlight python >}}
import secrets

length = 50
chars = 'abcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*(-_=+)'

secret_key = ''.join(secrets.choice(chars) for i in range(length))

print(secret_key)
{{< / highlight >}}
