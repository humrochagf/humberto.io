+++
description = "Um breve conto sobre a jornada de publicar meu primeiro jogo"
draft = true
images = ["/img/first-game/pyxel-lander-splash-screen.png"]
linkTitle = ""
publishDate = "2019-08-27T03:00:00+00:00"
slug = "publicando-meu-primeiro-jogo"
tags = ["python", "pyxel", "gamedev"]
title = "Publicando meu primeiro Jogo"

+++
Jogos sempre me conectam com tecnologia desde o início.

Eu e meu pai montamos nosso primeiro computador (um Pentium 286), e a primeira coisa que eu me lembro de fazer foi jogar os jogos de DOS como Prince of Persia e Lunar Lander. Eu aprendi vários comandos de CLI só para poder jogar os meus jogos favoritos.

A paixão por jogar e fazer jogos sempre me acompanhou como um hobby. I tenho uma [série de posts sobre pygame](https://humberto.io/pt-br/tags/pygame/) neste blog onde eu passo pelos conceitos básicos de desenvolvimento de jogos tentando explicar para pessoas que estejam iniciando seu aprendizado na área.

O tempo foi passando e a correria do dia a dia consumiu boa parte da minha energia e minha série de postagens ficou sem atualização por um bom tempo, bem como meu hobby.

Então, em dezembro do ano passado durante o feriado eu descobri o [Pyxel](https://github.com/kitao/pyxel). É um motor de desenvolvimento de jogos estilo retro, escrito em Python e que segue as limitações do [pico-8](https://en.wikipedia.org/wiki/Pico-8), além de já vir com um editor de sprites, tilemap e música.

Eu amo jogos com estilo pixel art, então eu decidi aproveitar o feriado para escrever meu primeiro jogo completo do zero até sua publicação. Ele seria um tributo ao antigo Lunar Lander de DOS que eu jogava quando era criança.

{{< videogif "/img/first-game/pyxel-lander-landing.webm" >}}

The theme choice was meaningful to me, not only because I played when I was little, but also because I've coded it with a friend at one of my [projects](https://github.com/ravishi/lunar-lander-ex/commits/master) at my Computer Science course.

The process of building the game was quite fun, and at the end of the day, I got the lunar module flying on the screen. The day after I dove into the procedural moon surface building and then when I was about to figure out the collision detection my holiday time was over, the day to day rush came back and my project stale.

More time passed, during a lunch break, I answered a tweet with the following question:

{{< tweet 1162932464169472001 >}}

I love coding and blogging and usually have fun on my day to day coding, but something 100% for fun reminded me that Christmas codding day so I answered:

{{< tweet 1163084951518175232 >}}

I've had uncountable RPG Maker unfinished games when I was young, and many others with different tools. What is so different from my work, or from my Open Source projects that makes me finish then, but not my Game projects?

I decided to finish the game to understand what was holding me back and... I've got the game finished, and published \\o/. You can check it out and play it at:

<iframe class="itchio" src="[https://itch.io/embed/471797?linkback=true](https://itch.io/embed/471797?linkback=true "https://itch.io/embed/471797?linkback=true")" width="552" height="167" frameborder="0"></iframe>

Also, I released it as Open Source, and anyone interested to see the code can go to [https://github.com/humrochagf/pyxel-lander](https://github.com/humrochagf/pyxel-lander "https://github.com/humrochagf/pyxel-lander")

## Wrapping Up

Going through this whole process made me realize somethings:

* Game development can be like a hydra. When you are solving one thing, your mind is creating new features on the go, so like any other field of software development, focus on the [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product), and keep new features to the next releases or projects.
* If it's your first game, try to not over complicate things, it's more important to go through the whole experience from the idea to the publishing, instead of making the perfect game.
* Tools with clear and limited scope like Pyxel are great to make your first game. It helps you a lot to keep it simple.
* Call your friends when you need some help. Thanks [Jairo Jair](https://jairojair.com/) for the help with the MacOS build.
* The coding just for fun thing is fantastic to learn new things and have fun in the process, let's do more of those.