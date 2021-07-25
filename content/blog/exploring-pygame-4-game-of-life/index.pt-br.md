---
title: "Desbravando o pygame 4 - Game of Life"
description: "Demonstrando a implementação do game of life, feita para o dojo"
publishDate: 2017-08-27
slug: desbravando-o-pygame-4-game-of-life
tags:
  - python
  - pygame
  - gamedev
images:
  - /img/posts/exploring-pygame-4-game-of-life.png
aliases:
  - exploring-pygame-4-game-of-life
  - /blog/desbravando-o-pygame-4-game-of-life

---

A um tempo atrás eu fiz uma postagem sobre [como organizar um coding dojo](/pt-br/blog/coding-dojo-101/) publicada logo após um dojo de aniversário da comunidade python aqui do DF no qual eu preparei o desafio de implementar o [jogo da vida](https://pt.wikipedia.org/wiki/Jogo_da_vida) (game of life) do matemático John Horton Conway.

Para deixar este desafio mais interativo preparei um simulador visual com pygame para testar nossa implementação. Este simulador utiliza de todos os conceitos que vimos até agora nesta série e aproveitando o interesse que ressurgiu no Grupy-DF sobre o assunto decidi reforçar os tópicos até então apresentados com a demonstração desta implementação.

## O Jogo

O jogo da vida é um jogo sem jogadores, que representa a dinâmica de interação entre um grupo de seres vivos em um plano bidimensional, partindo de uma configuração inicial e seguindo um conjunto de quatro regras simples.

Este plano possuí um formato tabular dividido em células, sendo que cada célula deste plano pode assumir dois estados, vivo ou morto. Além disso este plano tem dimensões infinitas, porém, limitaremos nosso plano a um recorte finito para simplificar nossa implementação.

A  configuração inicial é chamada de semente (seed) e pode ser pré-definida ou aleatória sendo ela alterada a cada ciclo (geração) pelas regras de interação que são as seguintes:

1. Toda célula com menos de dois vizinhos vivos morre por baixa população;

    ![regra um](assets/rule-one.png)

2. Toda célula com dois ou três vizinhos vivos sobrevive para a próxima geração;

    ![regra dois](assets/rule-two.png)

3. Toda célula com mais de três vizinhos vivos morre por superpopulação;

    ![regra tres](assets/rule-three.png)

4. Toda célula morta com exatamente três vizinhos vivos se torna uma célula viva por reprodução.

    ![regra quatro](assets/rule-four.png)

Não há interação entre gerações, ou seja, se por exemplo uma célula nasce, ao se calcular a célula vizinha ela não é levada em conta pois ela não existia na geração vigente.

## Implementação

O simulador foi feito para testar a lógica do jogo e se encontra no arquivo `simulator.py` enquanto no arquivo `game.py` fica o código responsável pela implementação do game of life. Sendo assim, analisaremos primeiramente o arquivo `game.py`, que não é o que implementamos no dia do dojo pois não concluímos toda a implementação a tempo, mas é uma versão que eu tinha criado previamente para testar o simulador e demonstrar ao final do dojo caso não chegássemos a implementação completa.

O desafio foi o seguinte, criar uma função `game_of_life` que recebesse uma seed em forma de uma matriz 50x50 e devolvesse a geração seguinte desta matriz como resultado:

{{< highlight python "linenos=table" >}}
# -*- coding: utf-8 -*-

def cell_check(section):
    '''
    Executa as regras do game of life em um recorte 3x3 para
    saber o estado da célula central
    '''
    # contador de vizinhos
    neighbors = 0
    # referência para o centro do recorte
    center = section[1][1]

    # somando todos os elementos do grupo
    for row in section:
        for cell in row:
            neighbors += cell

    # removendo o valor da célula central para que sobre somente
    # a soma dos vizinhos
    neighbors -= center

    # aplicando as regras do game of life
    # note que a regra dois não precisa ser ativamente aplicada, pois
    # ela não altera o estado da célula avaliada
    if neighbors <= 1:
        # menos de dois vizinhos a célula central morre por baixa população
        center = 0
    elif neighbors == 3:
        # exatamente três a célula nasce por reprodução
        center = 1
    elif neighbors >= 4:
        # mais que três a célula morre de super população
        center = 0

    # retorna o valor da célula central
    return center


def get_section(matrix, row, col):
    '''
    Extraí um recorte da vizinhança 3x3 em um plano
    dada a coordenada da célula central
    '''
    # monta um plano 3x3 somente com células mortas para fazer uma cópia
    # da área a ser analizada
    section = [[0 for _ in range(3)] for _ in range(3)]

    # percorre as redondezas da célula de posição row x col
    # copiando seu valor para section
    for sec_r, r in enumerate(range(row-1, row+2)):
        for sec_c, c in enumerate(range(col-1, col+2)):
            section[sec_r][sec_c] = matrix[r % 50][c % 50]

    # devolve o recorte 3x3 do plano
    return section


def game_of_life(seed):
    '''
    Recebe uma seed de um plano 50x50 executa o game of life e devolve
    a geração seguinte
    '''
    # cria um plano vazio para armazenar a nova geração pois não podemos
    # operar diretamente na geração corrente para não gerar efeito colateral
    next_gen = [[0 for _ in range(50)] for _ in range(50)]

    # percorre o plano tirando recortes 3x3 da vizinhança da célula central
    # e os avaliando para descobrir a geração seguinte de cada célula
    for r, row in enumerate(seed):
        for c, col in enumerate(row):
            next_gen[r][c] = cell_check(get_section(seed, r, c))

    # devolve a geração seguinte
    return next_gen
{{< / highlight >}}

Como visto na implementação o código de `game.py` executa somente uma geração do game of life, o que é excelente para o nosso `simulador.py`, pois ele utilizará do nosso conceito de loop de jogo e a cada ciclo atualiza o jogo com a geração seguinte e desenha seu estado na tela:

{{< highlight python "linenos=table" >}}
# -*- coding: utf-8 -*-

import random
import time

import pygame

from game import game_of_life

# plano vazio
SEED = [[0 for _ in range(50)] for _ in range(50)]

# Glider
SEED[23][24] = 1
SEED[24][25] = 1
SEED[25][23] = SEED[25][24] = SEED[25][25] = 1

# Glider gun
# SEED[20][30] = 1
# SEED[21][28] = SEED[21][30] = 1
# SEED[22][18] = SEED[22][19] = SEED[22][26] = SEED[22][27] = SEED[22][40] = SEED[22][41] = 1
# SEED[23][17] = SEED[23][21] = SEED[23][26] = SEED[23][27] = SEED[23][40] = SEED[23][41] = 1
# SEED[24][6]  = SEED[24][7]  = SEED[24][16] = SEED[24][22] = SEED[24][26] = SEED[24][27] = 1
# SEED[25][6]  = SEED[25][7]  = SEED[25][16] = SEED[25][20] = SEED[25][22] = SEED[25][23] = SEED[25][28] = SEED[25][30] = 1
# SEED[26][16] = SEED[26][22] = SEED[26][30] = 1
# SEED[27][17] = SEED[27][21] = 1
# SEED[28][18] = SEED[28][19] = 1

# Rich's p16
# SEED[19][20] = SEED[19][21] = SEED[19][22] = SEED[19][26] = SEED[19][27] = SEED[19][28] = 1
# SEED[20][19] = SEED[20][23] = SEED[20][25] = SEED[20][29] = 1
# SEED[21][19] = SEED[21][23] = SEED[21][25] = SEED[21][29] = 1
# SEED[22][18] = SEED[22][20] = SEED[22][21] = SEED[22][22] = SEED[22][23] = SEED[22][25] = SEED[22][26] = SEED[22][27] = SEED[22][28] = SEED[22][30] = 1
# SEED[23][18] = SEED[23][19] = SEED[23][29] = SEED[23][30] = 1
# SEED[26][22] = SEED[26][23] = SEED[26][22] = SEED[26][25] = SEED[26][26] = 1
# SEED[27][21] = SEED[27][23] = SEED[27][25] = SEED[27][27] = 1
# SEED[28][22] = SEED[28][26] = 1

# Random
# SEED = [[random.choice([0, 1]) for _ in range(50)] for _ in range(50)]

pygame.init()

screen = pygame.display.set_mode((550, 550))


def draw_matrix(matrix):
    '''
    Função para desenhar o plano
    '''

    # preenche a tela de preto
    screen.fill([0, 0, 0])

    # percorre o plano desenhando as células com seus valores
    for r, row in enumerate(matrix):
        for c, cell in enumerate(row):
            if cell:
                # caso a célula esteja viva, a pinte de branco
                pygame.draw.rect(
                    screen, (255, 255, 255), (11*c, 11*r, 10, 10)
                )


# define a seed como um dos valores de exemplo do começo
seed = SEED

# desenha o estado inicial de nossa seed
draw_matrix(seed)

pygame.display.flip()

# espera por 1 segundo para podermos contemplar o estado de partida
time.sleep(1)

while True:
    # PROCESSAMENTO DE ENTRADA
    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        # finaliza o programa caso clique em fechar
        break
    elif (event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE):
        # finaliza o programa caso pressione a tecla ESC
        break

    # ATUALIZAÇÃO DO JOGO

    # aplica o game of life para processar a geração seguinte
    seed = game_of_life(seed)

    # DESENHO

    # desenha a nova geração na tela
    draw_matrix(seed)

    pygame.display.flip()

    # espera um breve momento antes de partir para o próximo ciclo
    time.sleep(0.05)
{{< / highlight >}}

Desenhamos uma vez a seed na tela antes de iniciar o loop para dar tempo do usuário ver o estado inicial na tela. Em seguida damos início ao loop de jogo checando por eventos como feito anteriormente mas com a adição da condição de parada utilizando a tecla `ESC` para finalizar o programa. Na etapa de atualização do jogo rodamos a função `game_of_life` do arquivo `game.py` para processar a geração seguinte e finalizamos com a etapa de desenho, agora com um tempo de espera menor entre os ciclos para ter uma visão mais dinâmica da evolução da nossa seed.

## Exemplos

Para demonstrar o simulador em funcionamento utilizei o [glider](https://goo.gl/jzm5UZ) que é o padrão mais famoso dentro do game of life e foi absorvido pela comunidade hacker como seu emblema. Ele é o menor padrão cíclico que realiza deslocamento pelo, plano:

{{< videogif "assets/glider.webm" >}}

{{< tip class="info" >}}
Obs.: Note que existem alguns padrões comentados no código do simulador, você pode remover o comentário de qualquer um deles para ver padrões diferentes em ação.
{{< /tip >}}

## Conclusão

O game of life é um conceito muito bom para se aprender conceitos da computação, recomendo assistir as entrevistas com seu criador pelo canal [Numberphile](https://www.youtube.com/playlist?list=PLt5AfwLFPxWIL8XA1npoNAHseS-j1y-7V) no youtube.

E como sempre o código está disponível no repositório [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/04-game-of-life) para todos.
