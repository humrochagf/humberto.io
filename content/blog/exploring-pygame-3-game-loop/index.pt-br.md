---
title: "Desbravando o pygame 3 - Game Loop"
description: "Saiba mais sobre como funciona o game loop e como implementa-lo no pygame"
publishDate: 2017-08-24
slug: desbravando-o-pygame-3-game-loop
tags:
  - python
  - pygame
  - gamedev
images:
  - /img/posts/exploring-pygame-3-game-loop.pt-br.png
aliases:
  - exploring-pygame-3-game-loop
  - /blog/desbravando-o-pygame-3-game-loop

---

Agora que sabemos como desenhar na tela ([postagem anterior](/pt-br/blog/desbravando-o-pygame-2-desenhando-na-tela/)) seria interessante que nosso jogo ficasse rodando até que alguém o feche. Para isso vamos utilizar um dos fundamentos do desenvolvimento de jogos que é o Game Loop.

## Conceito

Dentro da computação o conceito de ciclo é bastante recorrente, trata-se de uma sequência de ações e tomadas de decisão programadas que se repetem dentro de um laço de repetição (ou loop). No baixo nível temos o processador do computador que trabalha em ciclos de operações aritméticas gerenciadas pelo clock, em sistemas operacionais o ciclo é quem gerencia o tempo de uso do processador, o dividindo entre todas as aplicações do sistema além de verificar se o usuário realizou algum comando, em servidores web usamos um ciclo para verificar se alguém requisitou uma página que será processada e devolvida em forma de resposta. Com jogos não seria diferente.

O ciclo dentro do desenvolvimento de jogos é chamado de Game Loop e é peça fundamental na estrutura de um jogo. De modo simplificado temos a seguinte estrutura:

![loop basico](assets/game-loop.pt-br.png)

Durante a execução jogo está sempre:

- **Recebendo e processando entradas do usuário** que são detectados por eventos gerados por dispositivos de entrada como um teclado e um mouse.

- **Atualizando o jogo** de acordo com os eventos processados e de outras mecânicas como aplicação de gravidade, detecção de colisão, eventos programados dentre outros.

- **Desenhando na tela** o resultado de toda a interação anterior.

Diferente de um loop de eventos de uma aplicação de desktop por exemplo em que o programa fica parado até que você tome alguma ação, no loop de jogo continua sua execução independente da entrada do usuário atualizando elementos independentes como, música, física, inteligência artificial, etc.

Um outro ponto importante que não está nos itens acima mas está presente na grande maioria dos jogos é o controle de atualização do loop. Mas este tópico ficará para uma próxima postagem.

## Implementação

Para demonstrar o game loop em funcionamento faremos um programa que desenha uma bola que quica pelos cantos da tela:

```python {linenos=table}
# -*- coding: utf-8 -*-

import pygame

# definindo cores
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Game Loop')

# variáveis da bola
position_x = 300
position_y = 200
velocity_x = 1
velocity_y = 1

# iniciando o loop de jogo
while True:
    # PROCESSAMENTO DE ENTRADA

    # capturando eventos
    event = pygame.event.poll()
    # caso o evento QUIT (clicar no x da janela) seja disparado
    if event.type == pygame.QUIT:
        # saia do loop finalizando o programa
        break

    # ATUALIZAÇÃO DO JOGO

    # movendo a bola
    position_x += velocity_x
    position_y += velocity_y

    # mudando a direção no eixo x nas bordas
    if position_x > 600:
        velocity_x = -1
    elif position_x < 0:
        velocity_x = 1

    # mudando a direção no eixo y nas bordas
    if position_y > 440:
        velocity_y = -1
    elif position_y < 0:
        velocity_y = 1

    # DESENHO

    # preenchendo o fundo com preto
    screen.fill(BLACK)

    # desenhando a bola
    pygame.draw.ellipse(screen, RED, [position_x, position_y, 40, 40])

    # atualizando a tela
    pygame.display.flip()
```

É assim, bem direto ao ponto, o loop definido em `while True:` irá rodar o código indefinidamente realizando as etapas de processamento de entrada, atualização do jogo e desenho a cada ciclo que passa.

Na etapa de processamento de entrada usamos `pygame.event.poll()` para recuperar um evento da fila e checar se ele é um evento de término de programa. Se ele for, usamos `break` para sair do loop, finalizando assim o programa.

Durante a etapa de atualização do jogo movemos a nossa bola no eixo x e y verificando se a bola se encontra nas extremidades e alterando sua direção em caso positivo.

E finalmente na etapa de desenho usamos as funções [previamente apresentadas](/pt-br/blog/desbravando-o-pygame-2-desenhando-na-tela/) para mostrar nossa bola na tela.

{{< tip class="info" >}}
Note que sempre pintamos o fundo de preto, caso contrário o rastro da bola ficaria na tela.
{{< /tip >}}

## Concluindo

E com isso finalizamos nosso breve passeio pelo game loop. Não se esqueça que todo código estará disponível no repositório [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/03-game-loop).
