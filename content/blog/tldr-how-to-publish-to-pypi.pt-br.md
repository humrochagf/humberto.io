---
title: "TLDR: Publicando no PyPI"
description: "Publique seu primeiro pacote no Python Package Index (PyPi)"
publishDate: 2018-03-05
slug: tldr-publicando-no-pypi
tags:
  - tldr
  - python
  - pypi
images:
  - /img/generics/pypi.png
aliases:
  - /blog/tldr-publicando-no-pypi

---

Já se perguntou como publicar seu próprio pacote Python para instalá-lo com o [pip](https://pypi.python.org/pypi/pip)? É menos complicado que parece, e qualquer pessoa pode fazer.

Basta acessar o [Python Package Index (PyPI)](https://pypi.python.org/pypi) e criar uma conta na qual irá registrar seus pacotes.

Em seguida crie um arquivo python chamado `setup.py` alterando os valores que forem necessários:

{{< highlight python >}}
#!/usr/bin/env python
# -*- coding: utf-8 -*-

from setuptools import find_packages, setup

setup(
    name='meu-pacote-python',
    packages=find_packages(),
    version='1.0.0',
    description='Descrição curta do meu pacote',
    long_description='Longa descrição do meu pacote',
    author='Meu Nome',
    author_email='meu@email.com',
    url='https://github.com/usuario/meu-pacote-python',
    install_requires=['dependencia1', 'dependencia2'],
    license='MIT',
    keywords=['dev', 'web'],
    classifiers=[
        'Intended Audience :: Developers',
        'License :: OSI Approved :: MIT License',
        'Natural Language :: English',
        'Programming Language :: Python :: 2',
        'Programming Language :: Python :: 3',
    ],
)
{{< / highlight >}}

O `find_packages` é uma ferramenta muito boa para encontrar automaticamente os arquivos que fazem parte do seu pacote qualquer dúvida veja sua documentação clicando [aqui](http://setuptools.readthedocs.io/en/latest/setuptools.html#using-find-packages).

Os `classifiers` servem para ajudar usuários a encontrar seu pacote na busca e a lista completa deles pode ser vista neste [link](https://pypi.python.org/pypi?%3Aaction=list_classifiers).

## Empacotando

Antes de publicar o pacote é preciso empacotá-lo e a forma mais simples é fazendo o empacotamento do código fonte rodando:

{{< highlight console >}}
$ python setup.py sdist
{{< / highlight >}}

{{< tip class="info" >}}
Este modelo de empacotamento requer que o pip rode uma etapa de montagem após a instalação
{{< /tip >}}

Para já empacotar o código montado rode:

{{< highlight console >}}
$ python setup.py bdist_wheel
{{< / highlight >}}

{{< tip class="info" >}}
Este comando empacota a versão montada para a versão do python em que você rodou o comando, para montar para python 2 e 3 rode duas vezes o comando (uma para cada versão do python)
{{< /tip >}}

Com isso você terá tudo empacotado e pronto para a publicação na pasta `dist/`.

## Publicando

Para publicar é preciso instalar o [twine](https://github.com/pypa/twine) que é uma ferramenta para publicar pacotes no PyPI:

{{< highlight console >}}
$ pip install twine
{{< / highlight >}}

Em seguida basta rodar o seguinte comando:

{{< highlight console >}}
$ twine upload --username usuario-no-pypi dist/meu-pacote-1.0.0.tar.gz dist/meu-pacote-1.0.0-py2-none-any.whl dist/meu-pacote-1.0.0-py3-none-any.whl
{{< / highlight >}}

{{< tip class="info" >}}
Não se esqueça de trocar o username e os nomes de arquivo para os seus.
{{< /tip >}}

O comando vai pedir a senha e em seguida fazer o upload para o PyPI.

## Bônus - Versionamento

Uma prática muito comum é versionar o código e adicionar algumas informações no `__init__.py` desta forma:

{{< highlight python >}}
__author__ = 'Meu Nome'
__email__ = 'meu@email.com'
__version__ = '1.0.0'
{{< / highlight >}}

Adicione este código no `setup.py` para usar os dados do `__init__.py` no seu pacote:

{{< highlight python >}}
import os
import re

package = 'meu_pacote'

with open(os.path.join(package, '__init__.py'), 'rb') as f:
    init_py = f.read().decode('utf-8')

version = re.search(
    '^__version__ = [\'\"]([^\'\"]+)[\'\"]', init_py, re.MULTILINE
).group(1)
author = re.search(
    '^__author__ = [\'\"]([^\'\"]+)[\'\"]', init_py, re.MULTILINE
).group(1)
email = re.search(
    '^__email__ = [\'\"]([^\'\"]+)[\'\"]', init_py, re.MULTILINE
).group(1)
...
setup(
    ...
    version=version,
    author=author,
    author_email=email,
    ...
)
{{< / highlight >}}

## Bônus - README.md como long_description

Quem publica código no [github](https://github.com) costuma escrever um arquivo **readme** com a descrição do projeto. Você pode usá-lo para não se repetir na `long_description`:

{{< highlight python >}}
with open('README.md', 'rb') as f:
    readme = f.read().decode('utf-8')

setup(
    ...
    long_description=readme,
    long_description_content_type="text/markdown",
    ...
)
{{< / highlight >}}
