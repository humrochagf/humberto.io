+++
description = "Um breve conto sobre a jornada de publicar meu primeiro jogo"
images = ["/img/first-game/pyxel-lander-splash-screen.png"]
linkTitle = ""
publishDate = "2019-08-28T00:00:00-03:00"
slug = "publicando-meu-primeiro-jogo"
tags = ["python", "pyxel", "gamedev"]
title = "Publicando meu primeiro Jogo"

+++
Jogos sempre me conectam com tecnologia desde o início.

Eu e meu pai montamos nosso primeiro computador (um Pentium 286) e a primeira coisa que eu me lembro de fazer, foi jogar os jogos de DOS como Prince of Persia e Lunar Lander. Eu aprendi vários comandos de CLI só para poder jogar os meus jogos favoritos.

A paixão por jogar e fazer jogos sempre me acompanhou como um hobby. I tenho uma [série de posts sobre pygame](https://humberto.io/pt-br/tags/pygame/) neste blog onde eu passo pelos conceitos básicos de desenvolvimento de jogos tentando explicar para pessoas que estejam iniciando seu aprendizado na área.

O tempo foi passando e a correria do dia a dia consumiu boa parte da minha energia e minha série de postagens ficou sem atualização por um bom tempo, bem como meu hobby.

Então, em dezembro do ano passado durante o feriado eu descobri o [Pyxel](https://github.com/kitao/pyxel). É um motor de desenvolvimento de jogos estilo retro, escrito em Python e que segue as limitações do [pico-8](https://en.wikipedia.org/wiki/Pico-8), além de já vir com um editor de sprite, tilemap e música.

Eu amo jogos com estilo pixel art, então eu decidi aproveitar o feriado para escrever meu primeiro jogo completo do zero até sua publicação. Ele seria um tributo ao antigo Lunar Lander de DOS que eu jogava quando era criança.

{{< videogif "/img/first-game/pyxel-lander-landing.webm" >}}

A escolha do tema foi significante para mim, não só por ser um dos primeiros jogos que eu joguei no computador, mas também por eu já ter programado uma versão em C dele com um amigo em um [projeto de faculdade](https://github.com/ravishi/lunar-lander-ex/commits/master).

O processo de criação do jogo foi bem divertido, e no final do dia, eu tinha o módulo lunar voando na tela. No dia seguinte eu mergulhei na geração procedural da superfície da lua e quando eu estava quase compreendendo a detecção de colisão meu feriado acabou, a correria do dia a dia retornou e o projeto estagnou.

O tempo foi passando e, durante uma pausa para o almoço, eu respondi um tweet fazendo a seguinte pergunta:

{{< tweet 1162932464169472001 >}}

Eu amo programar e escrever no meu blog e na maior parte do tempo me divirto fazendo isso no meu dia a dia, mas pensando em algo 100% por diversão me lembrou aqueles dias de programação no Natal, então respondi:

{{< tweet 1163084951518175232 >}}

Já perdi as contas de quantos jogos de RPG Maker incompletos eu tive quando era mais novo, além dos que tentei fazer com outras ferramentas. O que tem de tão diferente do meu trabalho e dos meus projetos Open Source que me impedia de concluir um jogo?

Decidi seguir em frente e concluir aquele jogo para entender o que estava me segurando e... Eu consegui concluir e publicar o jogo \\o/. Vocês podem dar uma olhada e jogá-lo em:

<iframe class="itchio" src="https://itch.io/embed/471797?linkback=true" width="552" height="167" frameborder="0"></iframe>

Eu também o publiquei como um projeto Open Source, então se alguém estiver interessado em dar uma olhada no código basta acessar [https://github.com/humrochagf/pyxel-lander](https://github.com/humrochagf/pyxel-lander "https://github.com/humrochagf/pyxel-lander")

## Resumindo

Passando por todo este processo me fez perceber algumas coisas:

* Desenvolvimento de jogos pode ser como uma hydra. Enquanto você está concluindo uma parte, sua mente está criando novas funcionalidades. Então, como em qualquer área de desenvolvimento de software, foque no [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) e deixe as novas funcionalidades para as próximas versões ou para o próximo projeto.
* Se este é o seu primeiro jogo, tente não complicar muito as coisas, neste momento é mais importante ter a experiência de sair de uma ideia até sua publicação do que fazer o jogo perfeito.
* Ferramentas com um escopo claro e limitado como Pyxel são ótimos para fazer o seu primeiro jogo. Isso te ajuda a manter as coisas bem simples.
* Chame seus amigos quando precisar de ajuda. Obrigado [Jairo Jair](https://jairojair.com/) pela ajuda com a build para MacOS.
* Programar só por diversão é fantástico para aprender coisas novas e se divertir no processo, vamos fazer mais disso.
