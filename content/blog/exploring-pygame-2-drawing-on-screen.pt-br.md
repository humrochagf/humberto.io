---
title: Desbravando o pygame 2 -  Desenhando na Tela
description: Aprenda como funciona o plano de desenho no pygame e como desenhar na tela
publishDate: 2017-02-03
images:
  - /img/exploring-pygame/drawing-axis.jpg
slug: desbravando-o-pygame-2-desenhando-na-tela
tags: ["python", "pygame", "gamedev"]
aliases: [
  "/blog/desbravando-o-pygame-2-desenhando-na-tela"
]
draft: false

---

Ao longo de um jogo estamos constantemente desenhando na tela. E agora que já sabemos criar um programa em pygame é hora de começar a desenhar.

## Plano de desenho

Voltando nas aulas de matemática do ensino fundamental fomos apresentados ao plano cartesiano. O plano cartesiano e um espaço bidimensional orientado pelos eixos x e y onde x aumenta para o lado direito e diminui para o lado esquerdo enquanto y aumenta para cima e diminui para baixo:

![plano cartesiano](/img/exploring-pygame/cartesian-axis.jpg)

No pygame a coisa muda um pouco, o plano de desenho consiste também de dois eixos onde x aumenta para a direita  e diminui para a esquerda, entretanto o y aumenta para baixo e diminui para cima, o inverso do plano cartesiano, sendo a área visível da tela um espaço que parte de do ponto `x=0` e `y=0` no topo esquerdo da tela até o tamanho determinado no comando `pygame.display.set_mode((x, y))`:

![plano de desenho](/img/exploring-pygame/drawing-axis.jpg)

## Desenhando

Crie um arquivo `draw.py` com o seguinte conteúdo:

{{< highlight python "linenos=table" >}}
# -*- coding: utf-8 -*-

import time

import pygame

# definindo cores
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

pygame.init()

screen = pygame.display.set_mode((640, 480))
# carregando fonte
font = pygame.font.SysFont(None, 55)

pygame.display.set_caption('Olá mundo')

# preenchendo o fundo com preto
screen.fill(BLACK)

# desenhando na superfície
pygame.draw.line(screen, WHITE, [10, 100], [630, 100], 5)
pygame.draw.rect(screen, BLUE, [200, 210, 40, 20])
pygame.draw.ellipse(screen, RED, [300, 200, 40, 40])
pygame.draw.polygon(screen, GREEN, [[420, 200], [440, 240], [400, 240]])

# atualizando a tela
pygame.display.flip()

time.sleep(5)

# preenchendo o fundo com preto
screen.fill(BLACK)

# definindo o texto
text = font.render('pygame', True, WHITE)
# copiando o texto para a superfície
screen.blit(text, [250, 200])

# atualizando a tela
pygame.display.flip()

time.sleep(5)
{{< / highlight >}}

Iniciamos nosso programa definindo os valores de cores para serem usados mais a frente. Em seguida inicializamos o pygame como feito anteriormente e carregamos uma fonte com `pygame.font.SysFont` para ser usada para escrever na tela.

Após preencher a superfície `screen` com a cor preta utilizamos algumas funções de desenho disponíveis em `pygame.draw`:

- **line:** desenha uma linha recebendo como parâmetros a superfície seguida da cor, uma lista com a coordenada de início e e uma lista com a coordenada de fim e a espessura da linha em pixels.
- **rect:** desenha um retângulo recebendo como parâmetros a superfície seguida da cor e uma lista com a posição inicial em x, posição inicial em y, largura, altura.
- **ellipse:** desenha uma circunferência recebendo como parâmetros a superfície seguida da cor e uma lista com a mesma estrutura do **rect**.
- **polygon:** desenha um polígono recebendo como parâmetros a superfície seguida da cor e uma lista de listas com as coordenadas dos vértices do polígono.

Em seguida `pygame.display.flip()` atualiza toda a tela o conteúdo desenhado previamente na superfície `screen`, seguido de um intervalo de 5 segundos.

Passado o tempo do `sleep`, preenchemos a superfície com a cor preta e definimos o texto que será escrito na tela com `font.render`, passando como primeiro parâmetro a `string` pygame, em segundo o valor `True` indicando para usar antialias que suaviza o contorno do texto e em terceiro o branco como cor. Este método cria uma superfície intermediária com o texto que será passado para nossa superfície `screen` através do método `blit` que recebe a superfície que será copiada e seu posicionamento em `screen`.

Por fim, atualizamos a tela com o novo conteúdo, esperamos mais 5 segundos e finalizamos nosso programa.

## Concluindo

Existem diversas formas de se desenhar na tela com pygame, você pode ver estes e outras funções de desenho com mais detalhes na [documentação](https://www.pygame.org/docs/ref/draw.html) da biblioteca.

O código deste capítulo encontra-se disponível no repositório [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/02-drawing).
