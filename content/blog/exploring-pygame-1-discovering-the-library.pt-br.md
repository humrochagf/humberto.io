---
title: Desbravando o pygame 1 - Conhecendo a Biblioteca
description: Um pouco sobre o que é o pygame, como instalar usar e escrever seu primeiro programa com esta biblioteca
publishDate: 2017-01-15
images:
  - /img/exploring-pygame/hello.png
slug: desbravando-o-pygame-1-conhecendo-a-biblioteca
tags: ["python", "pygame", "gamedev"]
aliases: [
  "/blog/desbravando-o-pygame-1-conhecendo-a-biblioteca"
]
draft: false

---

Desenvolver jogos é um lugar comum dentre muitas das pessoas que começam a estudar programação. Comigo não foi diferente, mesmo não seguindo a carreira de desenvolvimento de jogos esta sempre foi uma área que me chamou a atenção.

Estou criando esta série para aprender mais sobre desenvolvimento de jogos utilizando como ferramenta o [pygame](http://www.pygame.org/) e para compartilhar minhas descobertas no processo. Irei desbrava-lo partindo de seus princípios básicos até a criação de um pequeno jogo pong single player.

Para isso, usarei programação procedural com o objetivo de atingir com mais facilidade os iniciantes em programação, finalizando com a refatoração para introduzir os conceitos de orientação a objetos para quem ainda não está familiarizado. Esta série foi elaborada tendo como base python 3.

## Pygame

Pygame é um conjunto de módulos python para desenvolvimento de jogos. Ele adiciona funcionalidades em cima do [SDL](https://www.libsdl.org/) que é uma biblioteca multiplataforma que abstrai os componentes multimídia do computador como áudio e vídeo, permitindo o desenvolvimento com maior facilidade de programas que lidam com estes recursos como os jogos.

### Instalação

Antes de  prosseguir com a instalação você precisa do python instalado no sistema, caso não saiba como proceder veja os guias de instalação para [linux](http://python.org.br/instalacao-linux), [mac](http://python.org.br/instalacao-mac) e [windows](http://python.org.br/instalacao-windows).

O processo de instalação do pygame varia de acordo com o sistema operacional utilizado, no [site oficial](http://www.pygame.org/wiki/GettingStarted#Pygame Installation) existem algumas opções de instalação descritas.

Para instalar em sistemas linux baseados em debian você precisa instalar antes os pacotes externos ao python através de:

{{< highlight console >}}
$ sudo apt-get build-dep python-pygame
{{< / highlight >}}

Em seguida, para instalar o pygame rode o seguinte comando em sua virtualenv:

{{< highlight console >}}
$ pip install pygame
{{< / highlight >}}

Ou no próprio sistema:

{{< highlight console >}}
$ sudo pip install pygame
{{< / highlight >}}

### Olá mundo

Como de costume, ao aprender algo novo em programação vamos escrever um programa mínimo para verificar se está tudo funcionando e pronto para dar continuidade com o aprendizado.

Para isso criaremos um arquivo `hello.py` com o seguinte conteúdo:

{{< highlight python "linenos=table" >}}
# -*- coding: utf-8 -*-

import time

import pygame

pygame.init()

screen = pygame.display.set_mode([640, 480])

pygame.display.set_caption('Olá mundo')

screen.fill([0, 0, 0])

pygame.display.flip()

time.sleep(5)
{{< / highlight >}}

Para rodar basta executar:

{{< highlight console >}}
$ python hello.py
{{< / highlight >}}

O resultado será este:

![hello](/img/exploring-pygame/hello.png)

Ainda está longe de ser um jogo, mas já temos alguns conceitos básicos importantes a serem comentados.

As três primeiras linhas são comuns a maioria dos programas escritos em python. Na primeira, definimos o tipo de codificação de caracteres usado pelo interpretador python que no caso foi `utf-8`. Esta linha não é necessária em python 3, mas para que as pessoas que estejam testando em python 2 não tenham problemas com acentuação prefiro manter este costume. As outras duas são a importação da biblioteca padrão `time` e da `pygame` que é onde reside toda a mágica.

O comando `pygame.init()` inicializa todos os módulos que necessitam de inicialização dentro do pygame.

Em `pygame.display.set_mode` criamos uma superfície para a nossa janela do tamanho 640x480 seguido de `pygame.display.set_caption` onde atribuímos o texto "Olá mundo" para o título da janela.

Com a janela criada, utilizamos o comando `fill` da janela `screen` para preenche-la com a cor preta. A cor é passada em formato de lista de três elementos que consistem dos valores do RGB que variam de 0 a 255. Troque os valores desta lista e rode novamente o programa para ver que a cor da janela mudar.

O comando `pygame.display.flip` representa um conceito importante do pygame e do desenvolvimento de jogos em geral. Quando usamos comandos para desenhar na tela, na verdade estamos desenhando na superfície criada em de memória que representa uma porção da nossa tela. Para copiar o conteúdo dela e mostra-lo na tela é preciso utilizar o `flip` que é análogo a você fazer o desenho para uma pessoa e ao terminar virar (flip) a superfície em que você estava desenhando para que ela veja. O `flip` atualiza a tela com o conteúdo desenhado na superfície `screen`.

Para finalizar o comando `sleep` da biblioteca `time` faz o programa esperar 5 segundos antes de finalizar. Caso contrário o programa fecharia antes que você possa ver o resultado.

## Concluindo

Com isso, chegamos ao final deste primeiro capítulo apresentando a biblioteca e escrevendo nosso primeiro programa. O código deste capítulo encontra-se disponível neste repositório: [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/01-introduction).

Fique de olho nos próximos capítulos onde iremos descrever conceitos do desenvolvimento de jogos e sua aplicação dentro do pygame.
