+++
description = "Aprenda como movimentar os objetos na tela e escreva sua primeira detecção de colisão"
draft = true
images = []
publishDate = "2019-08-30T00:00:00-03:00"
slug = "desbravando-o-pygame-5-movimento-e-colisao"
tags = ["python", "pygame", "gamedev"]
title = "Desbravando o pygame 5 - Movimento e Colisão"

+++
O movimento é uma característica que está presente na maioria dos jogos. Ao saltar entre plataformas, atirar contra a horda de inimigos, pilotar uma nave espacial e correr pelas estradas estamos exercendo movimento, interagindo com o ambiente do jogo, aplicando ações e causando reações.

Neste capítulo iremos conhecer os conceitos básicos de movimentação de objetos na tela e de sua interação com outros elementos através da detecção de colisão.

## Movimento

Se você vem acompanhando esta série de postagens, você já viu um exemplo de movimentação na postagem sobre [game loop](https://humberto.io/pt-br/blog/desbravando-o-pygame-3-game-loop/) onde implementamos uma bola que se movimentava quicando pela tela.

Desta vez nós revisitaremos este código detalhando o processo de geração de movimento e também acrescentando novos conceitos.

Para começar, vamos escrever a forma mais básica de movimentação de um objeto na tela:

{{< highlight python "linenos=table,hl_lines=21 26" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
RED = pygame.Color(255, 0, 0)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Simple Movement')

position_x = 0

while True:
event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break
    
    # moves the ball one pixel per cicle
    position_x += 1
    
    screen.fill(BLACK)
    
    # draws the ball with the incremented position
    pygame.draw.ellipse(screen, RED, [position_x, 300, 40, 40])
    
    pygame.display.flip()

{{< / highlight >}}

O código é bem direto ao ponto, criamos a variável `position_x` para guardar a posição da bola no eixo x.

Dentro do loop sua posição é incrementada em um pixel a cada ciclo e a bola é desenhada novamente em sua nova posição.

Esta abordagem possuí um problema. Você não consegue ter controle sobre a velocidade de movimento da bola. Em computadores mais rápidos mais loops por segundo serão processados e nos mais lentos o contrário e eventualmente terá resultados como este:

{{< videogif "/img/exploring-pygame/fast-ball.webm" >}}

Para corrigir este problema precisamos voltar as aulas de física quando nos ensinaram sobre o **M**ovimento **R**etilíneo **U**niforme. Para garantirmos uma velocidade constante usaremos a seguinte fórmula:

S = Si + V*dT

Sua aplicação no código ficará desta forma:

{{< highlight python "linenos=table,hl_lines=16 19 24 26 28 36" >}}
import time

import pygame

BLACK = pygame.Color(0, 0, 0)
RED = pygame.Color(255, 0, 0)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Velocity')

position_x = 0
# 10 pyxels per second
velocity_x = 100

# capture the initial time
ti = time.time()

while True:
    # get how much time has passed
    # beetween the cycles
    tf = time.time()
    # calculate the delta
    dt = (tf - ti)
    # sets final time as the initial time
    ti = tf

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # moves the ball one pixel per cicle
    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.ellipse(screen, RED, [position_x, 300, 40, 40])

    pygame.display.flip()
{{< highlight >}}