+++
description = "Learn how to move the objects on the screen and write you first collision detection with pygame"
draft = true
images = [
  "/img/exploring-pygame/movement-collision.png"
]
katex = true
markup = "mmark"
publishDate = "2019-08-30T00:00:00-03:00"
tags = ["python", "pygame", "gamedev"]
title = "Exploring pygame 5 - Movement and Collision"

+++
Movement is part of a large portion of games. When jumping between platforms, shooting against a horde of enemies, piloting a space ship and running through the streets, we are causing movement and interacting with the game environment, applying action and causing reactions.

This chapter is to describe the basics of moving objects across the screen and their interaction with other elements through collision detection.

## Movement

If you are following this series of posts, you already saw an example of movement at the post about [the game loop](https://humberto.io/pt-br/blog/desbravando-o-pygame-3-game-loop/), where we implemented a ball that moves through the screen bouncing around.

This time we'll go through a similar code sample, adding more details to it and also, adding some new concepts:

{{< highlight python "linenos=table,hl_lines=21 26" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Simple Movement')

position_x = 0

while True:
    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # moves the square one pixel per cycle
    position_x += 1

    screen.fill(BLACK)

    # draws the square with the incremented position
    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

The code is pretty straight forward. We create a variable `position_x` to hold the square position on x.

Inside the loop, we increment its position by one pixel per cycle and then it's drawn again at its new position.

The drawing loop creates a cinematic effect of movement on the screen, but this implementation has a problem.

You can't control the object movement velocity, and powerful the computer, more loops are processed, causing results like that:

{{< videogif "/img/exploring-pygame/square-fast.webm" >}}

To fix this issue, we need to go back to physics class, when we learned about **linear motion**. To ensure constant velocity, we'll use the following formula:

$$d = d_{i} + v \Delta t$$

Applying it to the code, we'll have this:

{{< highlight python "linenos=table,hl_lines=16 19 24 26 28 36" >}}
import time

import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Velocity')

position_x = 0
# 100 pixels per second
velocity_x = 100

# capture the initial time
ti = time.time()

while True:
    # gets the time for
    # this cycle
    tf = time.time()
    # calculate the delta
    dt = (tf - ti)
    # sets final time as the initial time
    ti = tf

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # moves the square at the velocity defined
    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

We start defining the velocity at the x-axis to 100 pixels per second.

Then we get the initial time to be used at the delta time calculation.

Inside the loop, we capture the final time and at the next line, the `dt` (delta time) subtracting the final time by the initial time.

At **line 28**, the initial time receives the final time to be used at the next loop cycle.

In the end, we calculate the displacement at **line 36**.

{{< videogif "/img/exploring-pygame/square-velocity.webm" >}}

As we can see, it is possible to control the object movement velocity, but that solves only the visible part of the issue, the loop executes much more than the necessary generating noting more than overhead.

## FPS

Cada ciclo de desenho na tela é conhecido como **frame**, o controle de atualização de frames na tela é uma prática comum no universo do áudio visual, tendo como unidade de medida o **FPS** (**F**rames **P**er **S**econd).

Implementar este controle em seu jogo trás uma série de benefícios:

- Reduz o uso desnecessário dos recursos da máquina
- Facilita a sincronização de jogos multiplayer
- Diminui a [propagação de erro em operação de ponto flutuante](https://floating-point-gui.de/errors/propagation/) (existem técnicas para mitigar este tipo de problema mas se você está começando, não se preocupe com isso neste momento)
- Aumenta a previsibilidade e facilita o planejamento do seu jogo. Passa a ser possível saber quanta coisa eu consigo processar no intervalo de um frame para outro dado os requisitos mínimos para seu jogo.

A taxa de atualização tradicional para filmes é de 24fps, já em jogos ela costuma variar entre 30 e 60fps. No nosso caso utilizaremos uma taxa de atualização de 30fps.

{{< highlight python "linenos=table,hl_lines=16 19 24" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('FPS')

position_x = 0
# since pygame clock returns its value
# in milliseconds, we divide the velocity
# by 1000 to keep the 100 pixel per seconds
velocity_x = 0.1

# create pygame clock
clock = pygame.time.Clock()

while True:
    # call the clock tick for 30 fps
    # and store the delta time
    dt = clock.tick(30)

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

O controle de FPS foi simplificado graças a classe `Clock` do pygame, na verdade o código fica até mais curto. Começamos trocando a velocidade de `100` para `0.1`, pois diferentemente da biblioteca `time` do Python que trabalha com segundos, o `Clock` do pygame trabalha em milissegundos e para garantir a mesma velocidade de cem pixels por segundo precisamos dividir a velocidade por mil.

Em seguida, instanciamos `Clock` antes de entrar no loop, e chamamos sua função `tick` em seu interior, passando como argumento a quantidade de **FPS** para limitá-lo.

A função `tick` deve ser chamada a cada ciclo e caso o ciclo anterior tenha sido muito rápido ela suspende sua execução por um breve tempo para manter a frequência desejada. Como resultado a função retorna o delta de tempo entre esta e sua chamada anterior.

{{% tip class="info" %}}
Dê uma olhada na [documentação da função `tick`](https://www.pygame.org/docs/ref/time.html#pygame.time.Clock.tick), pois ela possuí uma diferença de precisão entre plataformas. Entretanto, existe uma função alternativa mais precisa (porém mais pesada) que pode realizar este trabalho caso esta precisão seja importante para o seu jogo.
{{% /tip %}}

Agora que temos o quadrado percorrendo a tela a uma velocidade constante podemos seguir para a etapa de detecção de colisão.

## Colisão

A colisão é o produto da interação dos objetos do seu jogo podendo ocorrer entre si ou com o ambiente. A detecção de colisão costuma crescer em complexidade na medida em que mais elementos de diferentes formatos são adicionados em cena.

No exemplo vamos nos ater aos conceitos básicos, fazendo o quadrado interagir com os limites da tela, mudando de direção ao colidir com suas extremidades:

{{< highlight python "linenos=table,hl_lines=12-19 33-34 36-38 42-47" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Collision')

# create the square Rect
square = pygame.Rect(300, 230, 20, 20)

# create the pads Rect
left_pad = pygame.Rect(20, 210, 20, 60)
right_pad = pygame.Rect(600, 210, 20, 60)

pads = [left_pad, right_pad]

velocity_x = 0.1

clock = pygame.time.Clock()

while True:
    dt = clock.tick(30)

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # use the move function inplace
    square.move_ip(velocity_x * dt, 0)

    # check for collision with the pads
    if square.collidelist(pads) >= 0:
        velocity_x = -velocity_x

    screen.fill(BLACK)

    # draw using the rect
    pygame.draw.rect(screen, WHITE, square)

    # draw the pads
    for pad in pads:
        pygame.draw.rect(screen, WHITE, pad)

    pygame.display.flip()
{{< / highlight >}}

A técnica de detecção de colisão mais simples consiste em tratar todos os elementos como áreas retangulares que no pygame esta mecânica é facilitada pela classe `Rect`, utilizada no código para criar uma área retangular para o quadrado e os pads com os quais irá se colidir.

Com a criação do `Rect`, passamos a usar a função `move_ip` para deslocá-lo. Esta função altera a posição de seu objeto, diferentemente da função `move` que retorna uma cópia de si com a posição alterada.

Na **linha 37** a função `collidelist` verifica se ocorreu alguma colisão com um dos elementos da lista, retornado seu índice em caso positivo e `-1` em caso negativo.

Por fim, o quadrado e os pads são desenhados na tela utilizando suas instâncias de `Rect` produzindo o resultado a seguir:

{{< videogif "/img/exploring-pygame/square-collision.webm" >}}

## Conclusão

Com estes conceitos de movimentação e colisão é possível criar jogos bem interessantes como o [Pong](https://pt.wikipedia.org/wiki/Pong). Vou encerrar esta postagem deixando como proposta que você utilize estes conceitos para implementá-lo.

Os códigos utilizados nesta postagem estão disponíveis em [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/05-movement-and-collision).
