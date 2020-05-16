+++
description = "Aprenda como movimentar os objetos na tela e escreva sua primeira detecção de colisão com pygame"
draft = false
images = [
  "/img/exploring-pygame/movement-collision.png"
]
publishDate = "2019-09-10T00:00:00-03:00"
slug = "desbravando-o-pygame-5-movimento-e-colisao"
tags = ["python", "pygame", "gamedev"]
title = "Desbravando o pygame 5 - Movimento e Colisão"
katex = true

+++
O movimento é uma característica que está presente na maioria dos jogos. Ao saltar entre plataformas, atirar contra a horda de inimigos, pilotar uma nave espacial e correr pelas estradas estamos exercendo movimento, interagindo com o ambiente do jogo, aplicando ações e causando reações.

Neste capítulo iremos conhecer os conceitos básicos de movimentação de objetos na tela e sua interação com outros elementos através da detecção de colisão.

## Movimento

Se você vem acompanhando esta série de postagens, teve um breve exemplo de movimentação na postagem sobre [game loop](https://humberto.io/pt-br/blog/desbravando-o-pygame-3-game-loop/), onde uma bola que se movimentava quicando pela tela foi implementada.

Desta vez veremos um código similar, detalhando o processo de movimentação, acrescentando novos conceitos. Começando pela forma mais básica de movimentação de um objeto na tela:

{{< highlight python "linenos=table,hl_lines=12 21 26" >}}
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

    # move o quadrado um pixel por ciclo
    position_x += 1

    screen.fill(BLACK)

    # desenha o quadrado em sua nova posição
    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

O código é bem direto, é criada uma variável `position_x` para guardar a posição do quadrado no eixo x.

Dentro do loop sua posição é incrementada em um pixel a cada ciclo ele é desenhado novamente em sua nova posição.

Este loop de desenho do objeto cria o efeito cinemático de deslocamento na tela, porém, esta implementação possuí um problema.

Não é possível controlar a velocidade de movimento do objeto e, em computadores mais potentes, mais loops por segundo serão processados causando eventualmente resultados como este:

{{< videogif "/img/exploring-pygame/square-fast.webm" >}}

Para corrigir este problema precisamos voltar as aulas de física quando nos ensinaram sobre o **MRU** (**M**ovimento **R**etilíneo **U**niforme) e, para garantirmos uma velocidade constante, usaremos a seguinte fórmula:

$$S = S_{i} + v \Delta t$$

Sua aplicação no código ficará assim:

{{< highlight python "linenos=table,hl_lines=16 19 24 26 28 36" >}}
import time

import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Velocity')

position_x = 0
# 100 pixels por segundo
velocity_x = 100

# captura o tempo inicial
ti = time.time()

while True:
    # captura o tempo
    # deste ciclo
    tf = time.time()
    # calcula o delta
    dt = (tf - ti)
    # atribui o tempo final como tempo inicial
    ti = tf

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # move o quadrado na velocidade média definida
    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

Começamos definindo a velocidade no eixo x para 100 pixels por segundo.

Em seguida, capturamos o tempo inicial para o cálculo do delta de tempo, que é quanto tempo se passou entre os ciclos do loop.

Dentro do loop, capturamos o tempo final e logo em seguida calculamos sua variação `dt` (delta de tempo) subtraindo o tempo final pelo inicial.

Na **linha 28** o tempo inicial passa a ser o tempo final para que possamos usá-lo no próximo ciclo.

Por fim calculamos o deslocamento que será feito na **linha 36**.

{{< videogif "/img/exploring-pygame/square-velocity.webm" >}}

Como podemos ver, agora é possível controlar a velocidade de movimentação dos objetos mas, isso só resolve a parte visível do problema, o loop continua sendo executado muito mais que o necessário. Nem o olho humano, nem a taxa de atualização do seu monitor conseguem acompanhar um volume exagerado de atualizações consecutivas além da sobrecarga desnecessária do processador.

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
# como o relógio do pygame trabalha
# em milissegundos, dividimos por 1000
# para manter os 100 pixels por segundo
velocity_x = 0.1

# criamos uma instância do relógio
clock = pygame.time.Clock()

while True:
    # chamamos o tick do relógio para 30 fps
    # e armazenamos o delta de tempo
    dt = clock.tick(30)

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

O controle de FPS foi simplificado graças a classe `Clock` do pygame, na verdade o código fica até mais curto.

Começamos trocando a velocidade de `100` para `0.1`, pois diferentemente da biblioteca `time` do Python que trabalha com segundos, o `Clock` do pygame trabalha em milissegundos e para garantir a mesma velocidade de cem pixels por segundo precisamos dividir a velocidade por mil.

Em seguida, instanciamos `Clock` antes de entrar no loop, e chamamos sua função `tick` em seu interior, passando como argumento a quantidade de **FPS** para limitá-lo.

A função `tick` deve ser chamada a cada ciclo e caso o ciclo anterior tenha sido muito rápido ela suspende sua execução por um breve tempo para manter a frequência desejada. Como resultado a função retorna o delta de tempo entre esta e sua chamada anterior.

{{% tip class="info" %}}
Dê uma olhada na [documentação da função `tick`](https://www.pygame.org/docs/ref/time.html#pygame.time.Clock.tick), pois ela possuí uma diferença de precisão entre plataformas. Entretanto, existe uma função alternativa mais precisa (porém mais pesada) que pode realizar este trabalho caso esta precisão seja importante para o seu jogo.
{{% /tip %}}

Agora que temos o quadrado percorrendo a tela a uma velocidade constante podemos seguir para a etapa de detecção de colisão.

## Colisão

A colisão é o produto da interação dos objetos do seu jogo podendo ocorrer entre si ou com o ambiente. A detecção de colisão costuma crescer em complexidade na medida em que mais elementos de diferentes formatos são adicionados em cena.

No exemplo vamos nos ater aos conceitos básicos, fazendo o quadrado interagir com os pads na tela, mudando de direção após sua colisão:

{{< highlight python "linenos=table,hl_lines=12-19 33-34 36-38 42-47" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Collision')

# cria o Rect para o quadrado
square = pygame.Rect(300, 230, 20, 20)

# cria o Rect para os pads
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

    # usa a função move inplace
    square.move_ip(velocity_x * dt, 0)

    # checa por colisão com os pads
    if square.collidelist(pads) >= 0:
        velocity_x = -velocity_x

    screen.fill(BLACK)

    # desenha o quadrado usando o Rect
    pygame.draw.rect(screen, WHITE, square)

    # desenha os pads
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

Com estes conceitos de movimentação e colisão é possível criar jogos bem interessantes como o [Pong](https://pt.wikipedia.org/wiki/Pong). Vou encerrar esta postagem deixando um desafio para você. Utilize estes conceitos para implementar uma versão completa do Pong.

Os códigos utilizados nesta postagem estão disponíveis em [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/05-movement-and-collision).
