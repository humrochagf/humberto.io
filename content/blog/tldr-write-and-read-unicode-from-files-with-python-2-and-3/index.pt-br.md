---
title: "TLDR: Leitura e escrita de Unicode em arquivos com Python 2 e 3"
description: "Leia e escreva caracteres Unicode em arquivos com um código que roda tanto em Python 2 quanto em Python 3"
publishDate: 2018-08-23
slug: tldr-leitura-e-escrita-de-unicode-em-arquivos-com-python-2-e-3
tags:
  - tldr
  - python
  - unicode
images:
  - /img/posts/tldr-write-and-read-unicode-from-files-with-python-2-and-3.pt-br.png
aliases:
  - tldr-write-and-read-unicode-from-files-with-python-2-and-3
  - /blog/tldr-leitura-e-escrita-de-unicode-em-arquivos-com-python-2-e-3

---

Conversando no Telegram com o [Mário Sérgio](https://twitter.com/queirozMario21) sobre problemas que surgem ao migrar código entre versões do Python me veio a ideia de escrever este **tldr** para ajudar quem precisa fazer leitura ou escrita de arquivos que contenham texto com caracteres [Unicode](https://pt.wikipedia.org/wiki/Unicode) que não estão presentes na tabela [ASCII](https://pt.wikipedia.org/wiki/ASCII) como caracteres acentuados, alfabetos diferentes do romano e [emoji](https://pt.wikipedia.org/wiki/Emoji), através de um código que funcione em ambas versões.

No Python 2 não existe uma distinção entre **byte** e **string**, o que faz com que códigos que lidam com entrada e saída sem o devido cuidado com codificação e decodificação funcionem sem nenhum erro aparente. Mas quando ele acontece deixa muita gente perdida com mensagens deste tipo:

{{< highlight console >}}
UnicodeDecodeError: 'ascii' codec can't decode byte 0xc3 in position 1: ordinal not in range(128)
{{< / highlight >}}

Mudando para Python 3, onde **byte** e **string** são tipos distintos, o código normalmente para de funcionar ou funciona de forma estranha, misturando o texto com a representação textual da codificação:

{{< highlight python >}}
'ol\xc3\xa1'
{{< / highlight >}}

A solução para escrever um código que rode em ambas versões sem comportamentos inesperados é seguir o [zen do python](https://www.python.org/dev/peps/pep-0020/) onde diz que explícito é melhor que implícito e sempre definir de forma clara o que é **byte** do que é **string** desta forma:

![encode decode](assets/encode-decode.pt-br.png)

O código abaixo abre um arquivo temporário para escrita em **byte** e escreve nele uma **string** que foi codificada em **byte** usando o padrão [utf-8](https://pt.wikipedia.org/wiki/UTF-8). Em seguida é feito o processo inverso, abrindo o arquivo para leitura em **byte** e decodificando seu conteúdo em **string** para imprimir na tela:

{{< highlight python >}}
# -*- coding: utf-8 -*-

from __future__ import print_function, unicode_literals

import tempfile

unicode_file = tempfile.mktemp()

# escrita
with open(unicode_file, 'wb') as f:
    f.write('こんにちは'.encode('utf-8'))

# leitura
with open(unicode_file, 'rb') as f:
    print(f.read().decode('utf-8'))
{{< / highlight >}}

O resultado é um código que roda tanto em Python 2 quanto em Python 3:

![rodando o código](assets/unicode-file.png)

{{< tip class="info" >}}
Esta dica também funciona para outros padrões de codificação como `latin-1` por exemplo
{{< /tip >}}
