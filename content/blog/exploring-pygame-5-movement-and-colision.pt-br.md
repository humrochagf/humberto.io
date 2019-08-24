+++
description = "Aprenda como movimentar os objetos na tela e escreva sua primeira detecção de colisão"
draft = true
images = []
publishDate = "2019-08-20T00:00:00-03:00"
slug = "desbravando-o-pygame-5-movimento-e-colisao"
tags = ["python", "pygame", "gamedev"]
title = "Desbravando o pygame 5 - Movimento e Colisão"

+++
O movimento é uma característica que está presente na maioria dos jogos. Ao saltar entre plataformas, atirar contra a horda de inimigos, pilotar uma nave espacial e correr pelas estradas estamos exercendo movimento, interagindo com o ambiente do jogo, aplicando ações e causando reações.

Neste capítulo iremos conhecer os conceitos básicos de movimentação de objetos na tela e de sua interação com outros elementos através da detecção de colisão.

## Movimento

Se você vem acompanhando esta série de postagens, você já viu um exemplo de movimentação na postagem sobre [game loop](https://humberto.io/pt-br/blog/desbravando-o-pygame-3-game-loop/) onde implementamos uma bola que se movimentava quicando pela tela.

Desta vez nós revisitaremos este código detalhando o processo de geração de movimento e também acrescentando novos conceitos.