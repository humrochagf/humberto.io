+++
description = "Aprenda como implementar sockets em Python do básico ao assíncrono"
draft = true
images = ["/img/generic/sockets.png"]
linkTitle = ""
publishDate = "2019-09-18T03:00:00+00:00"
slug = "python-sockets-do-basico-ao-async"
tags = ["sockets", "python", "async", "asyncio"]
title = "Python Sockets do Básico ao Async"

+++

A comunicação é uma das bases da computação, os processos estão constantemente se comunicando com o sistema operacional 

Uma das formas mais básicas de se comunicar com outros programas é através de um socket

## Socket

De modo geral **socket** é uma palavra que vem do inglês que é usada para denominar um buraco feito para envolver ou encaixar algo, como um ponto de conexão. Indo mais a fundo e buscando em sua etimologia a [origem mais primitiva](https://en.wiktionary.org/wiki/socket) da palavra quer dizer literalmente **focinho de porco**.

![Muppet Piggy Chocada](/img/memes/chocked.gif)

O que provavelmente motivou a adoção da palavra para designar o conector elétrico que fica na parede das nossas casas:

![imagem de uma tomada](/img/sockets/eletric-socket.jpg)

E em seguida, a computação adotou a palavra para denominar o ponto final no fluxo de comunicação entre dois processo em um computador ou entre computadores.

Dentro do modelo [TCP/IP](https://pt.wikipedia.org/wiki/TCP/IP) o socket é a ponte entre a camada de aplicação e a camada de transporte, é em cima dele que os protocolos de aplicação como o [HTTP](https://pt.wikipedia.org/wiki/Hypertext_Transfer_Protocol) são construídos.

Sockets podem ser utilizados tanto para a comunicação entre processos através da rede onde normalmente as comunicação são 
