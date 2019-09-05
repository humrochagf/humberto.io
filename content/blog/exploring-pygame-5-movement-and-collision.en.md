+++
description = "Learn how to move the objects on the screen and write you first collision detection with pygame"
draft = true
images = []
publishDate = "2019-08-30T00:00:00-03:00"
tags = ["python", "pygame", "gamedev"]
title = "Exploring pygame 5 - Movement and Collision"
katex = true
markup = "mmark"

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
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Simple Movement')

position_x = 0

while True:
event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # move a bola 1 pixel por ciclo
    position_x += 1

    screen.fill(BLACK)

    # desenha a bola na posição incrementada
    pygame.draw.ellipse(screen, WHITE, [position_x, 220, 40, 40])

    pygame.display.flip()
{{< / highlight >}}

O código é bem direto ao ponto, criamos a variável `position_x` para guardar a posição da bola no eixo x.

Dentro do loop sua posição é incrementada em um pixel a cada ciclo e a bola é desenhada novamente em sua nova posição.

Esta abordagem possuí um problema. Você não consegue ter controle sobre a velocidade de movimento da bola. Em computadores mais rápidos mais loops por segundo serão processados e nos mais lentos o contrário e eventualmente terá resultados como este:

{{< videogif "/img/exploring-pygame/ball-fast.webm" >}}

Para corrigir este problema precisamos voltar as aulas de física quando nos ensinaram sobre o **M**ovimento **R**etilíneo **U**niforme. Para garantirmos uma velocidade constante usaremos a seguinte fórmula:

$$S = S_{i} + v \Delta t$$

Sua aplicação no código ficará desta forma:

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

    # move a bola na velocidade média definida
    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.ellipse(screen, WHITE, [position_x, 220, 40, 40])

    pygame.display.flip()
{{< / highlight >}}

Começamos definindo a velocidade da bola no eixo x para 100 pixels por segundo na **linha 16**.

Em seguida na **linha 19** capturamos o tempo inicial para o cálculo do delta de tempo, que é quanto tempo se passou entre os ciclos do loop.

Entrando no loop capturamos o tempo final na **linha 24** e logo em seguida na **linha 26** calculamos o `dt` subtraindo o tempo final pelo inicial.

Na **linha 28** o tempo inicial passa a ser o tempo final para que possamos usá-lo no próximo ciclo.

Por fim calculamos o deslocamento que será feito na **linha 36**.

{{< videogif "/img/exploring-pygame/ball-velocity.webm" >}}

Como podemos ver agora é possível controlar a velocidade da bola. Porém, isso só resolve a parte visível do problema, o loop continua sendo executado muito mais que o necessário. Nem o olho humano, nem a taxa de atualização do seu monitor vai conseguir acompanhar um volume exagerado te atualizações consecutivas além da sobrecarga desnecessária do processador.

## FPS

O controle de atualização de tela é uma prática comum dentro do mundo do áudio visual e sua unidade de medida é o **FPS** (**F**rames **P**er **S**econd).

Ter este controle implementado em seu jogo trás uma série de benefícios:

- Reduz o uso desnecessário do uso dos recursos da máquina
- Facilita a sincronização de jogos multiplayer
- Diminui a as perdas em operação de ponto flutuante. Por realizar muitas operações com frações de tempo muito pequenas em uma frequência exagerada erros de operação de ponto flutuante crescem muito mais rápido (existem técnicas mais sofisticadas para mitigar este tipo de problema mas para quem está começando isso não se preocupe com isto neste momento)
- Aumenta a previsibilidade e facilita o planejamento do seu jogo. Coisas do tipo para um computador com os requisitos mínimos, quanta coisa eu consigo processar no intervalo de um frame para outro?

No desenvolvimento de jogos o mais comum é encontrar uma taxa de atualização entre 30 e 60 fps. No nosso caso utilizaremos uma taxa de atualização de 30fps.

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

    pygame.draw.ellipse(screen, WHITE, [position_x, 200, 40, 40])

    pygame.display.flip()
{{< / highlight >}}

Graças a implementação de relógio da classe `Clock` do pygame, não tivemos muito trabalho com a implementação, na verdade o código fica até mais curto. Primeiramente trocamos a velocidade de `100` para `0.1` na **linha 16**, pois diferentemente da biblioteca `time` do Python que trabalha com segundos, o `Clock` do pygame trabalha em milissegundos e para garantir a mesma velocidade de cem pixels por segundo precisamos dividir a velocidade por `1000`.

Na **linha 19** instanciamos o `Clock` e logo mais, na **linha 24** chamamos sua função `tick` passando como argumento a quantidade de **FPS** que queremos limitar nosso loop de jogo.

A função `tick` deve ser chamada a cada ciclo e caso o ciclo anterior tenha sido muito rápido ela para a execução do programa por um breve tempo para manter a frequência desejada. Como resultado, esta função retorna o delta de tempo entre esta e a vez anterior em que ela foi chamada.

{{% tip class="info" %}}
Dê uma olhada na [documentação da função `tick`](https://www.pygame.org/docs/ref/time.html#pygame.time.Clock.tick), ela possuí uma questão quanto a precisão entre plataformas, mas existe uma função alternativa mais precisa (porém mais pesada) que pode realizar este trabalho caso esta precisão seja importante para o seu jogo.
{{% /tip %}}

Agora que temos nossa bola percorrendo a tela a uma velocidade constante podemos seguir para a etapa de detecção de colisão.

## Colisão

A colisão é o produto da interação dos objetos do seu jogo. Esta interação pode ocorrer entre si e com o ambiente. A detecção de colisão costuma crescer em complexidade na medida em que mais elementos de diferentes formatos são adicionados em cena.

Em nosso exemplo vamos nos ater aos conceitos básicos fazendo a bola interagir com os limites da tela mudando de direção ao colidir com suas extremidades:

{{< highlight python "linenos=table,hl_lines=12-19 33-34 36-38 42-47" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Collision')

# cria o Rect para a bola
ball = pygame.Rect(300, 220, 40, 40)

# cria o Rect para os pads
left_pad = pygame.Rect(20, 200, 20, 80)
right_pad = pygame.Rect(600, 200, 20, 80)

pads = [left_pad, right_pad]

velocity_x = 0.1

clock = pygame.time.Clock()

while True:
    dt = clock.tick(30)

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # usa a função move inplace
    ball.move_ip(velocity_x * dt, 0)

    # checa por colisão com os pads
    if ball.collidelist(pads) >= 0:
        velocity_x = -velocity_x

    screen.fill(BLACK)

    # desenha a bola usando o Rect
    pygame.draw.ellipse(screen, WHITE, ball)

    # desenha os pads
    for pad in pads:
        pygame.draw.rect(screen, WHITE, pad)

    pygame.display.flip()
{{< / highlight >}}

A técnica de detecção de colisão mais simples é a de tratar todos os elementos como áreas retangulares e o pygame implementa esta mecânica através da classe `Rect` que foi utilizada a partir da **linha 12** onde foi criada uma área retangular para a bola seguida da criação de dois blocos com os quais a bola irá se colidir.

Com a criação do `Rect` para a bola, passamos a usar a função `move_ip` para deslocá-la na **linha 34**. Esta função altera a posição do objeto que a chama, diferentemente da função `move` que retorna uma cópia do objeto com sua posição alterada.

Na **linha 37** a função `collidelist` verifica se ocorreu alguma colisão com um dos elementos da lista, retornado seu índice em caso positivo e `-1` em caso negativo.

E por fim a bola e os pads são desenhados na tela utilizando suas instâncias de `Rect` produzindo o resultado a seguir:

{{< videogif "/img/exploring-pygame/ball-collision.webm" >}}

## Conclusão

Com estes conceitos de movimentação e colisão já é possível criar jogos bem interessantes como o [Pong](https://pt.wikipedia.org/wiki/Pong). Vou encerrar esta postagem deixando como proposta que você utilize estes conceitos para implementá-lo. 

Os códigos utilizados nesta postagem estão disponíveis em [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/05-movement-and-collision).
