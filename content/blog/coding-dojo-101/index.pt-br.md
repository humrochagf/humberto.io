---
title: "Coding Dojo 101"
description: "Descubra o que é e como organizar um Coding Dojo em sua faculdade ou comunidade local"
publishDate: 2016-12-02
tags:
  - dojo
  - learning
  - python
images:
  - /img/posts/coding-dojo-101.pt-br.jpg

---

Meu primeiro Coding Dojo aconteceu próximo ao meu primeiro contato com a linguagem Python na faculdade. Um colega de classe ([Dirley](https://github.com/ravishi)) tinha acabado de voltar da Python Brasil muito animado, pois tinha visto uma palestra sobre o tema e queria colocar em prática com o pessoal da turma. Aquele dia foi muito divertido e talvez um dos mais importantes momentos que me levaram a mergulhar de cabeça na linguagem Python.

Assim como me trouxe para a linguagem e posteriormente para a comunidade, o dojo pode ser uma porta de entrada para muita gente na programação.

### O que é o Coding Dojo?

Assim como nas artes marciais o dojo é um ambiente de aprendizado e este é um conceito que deve ser reforçado na cabeça dos participantes.

Além disso é um ambiente no qual é praticado o desenvolvimento orientado a testes ou TDD, a programação pareada e babysteps para resolver um desafio proposto pelo organizador.

A resolução do desafio não é obrigatória para um dojo de sucesso, o objetivo principal é e sempre será a diversão.

Este vídeo ilustra brevemente seu funcionamento:

{{< youtube vqnwQ3oVM1M >}}

Um dojo pode ser feito remotamente, mas o presencial é onde a mágica acontece de verdade.

### Requisitos

Para organizar um dojo não é preciso de muita coisa:

- Uma sala ou um ambiente com lugar para sentar
- Somente 1 computador onde os participantes iram programar
- Uma tela maior ou projetor para a platéia acompanhar a  programação

### A Dinâmica de um Coding Dojo

A dinâmica acontece seguindo este fluxo:

![a dinamica de um coding dojo](assets/flow.pt-br.jpg)

O fluxo é composto de ciclos de 5 a 7 minutos, este tempo é flexível, porém para iniciantes não é recomendado que seja muito extenso para evitar que a pessoa fique travada e para estimular mais pessoas a participar (eu pessoalmente sempre uso 5 minutos).

O piloto é o único que programa e não deve ser interrompido pela platéia durante cada ciclo, onde realiza programação pareada com o copiloto, que é a única pessoa que tem permissão para falar, ajudar e dar opinião.

A platéia assiste e não fala até o final de cada ciclo. Algumas pessoas gostam de fazer pequenas pausas quando os testes passam para discutir algo com a platéia, porém quando os testes estiverem falhando não é permitido nenhuma interação da platéia com a dupla no comando.

No intervalo de cada ciclo a dupla explica sua linha de pensamento e o que está fazendo, o piloto vai para a platéia, o copiloto assume a posição de piloto e um voluntário (de preferência quem ainda não programou) assume o copiloto.

Isso se repete durante todo o dojo, até a solução do desafio ou até o tempo máximo estipulado que costuma variar de 40 minutos até 1 hora e meia, mais que isso costuma ficar muito cansativo.

### Programação Pareada

A programação pareada é uma das metodologias utilizadas no dojo e ela consiste nada mais nada menos de duas pessoas trabalhando em conjunto na solução de um problema. Sempre um programando e o outro ajudando com o raciocínio, sugestões e dúvidas.

### Desenvolvimento Orientado a Testes

Outra metodologia essencial é o TDD, que consiste em escrever testes antes do código para resolver o seu problema. Se todos os testes estiverem passando você não tem o que programar, portanto é preciso escrever mais um teste que valide algo que ainda não foi implementado para que o teste falhe e lhe obrigue a escrever código para fazer aquele teste passar.

### Babysteps

Escrever os testes primeiro é um grande desafio para quem não possuí este costume, as pessoas já querem testar a solução do problema de cara e sair resolvendo tudo o que leva a uma experiência ruim do TDD. Você possuí dificuldade em fazer os testes passarem? Provavelmente você está testando muita coisa em um único teste.

A ideia do babysteps é andar a "passos de bebê", quebrando seu problema em pequenos passos facilitando a resolução e a compreensão para o copiloto e para a platéia.

### Finalizando o Coding Dojo

Ao término de um dojo é comum finalizar com um levantamento dos prós (o que foi legal), contras (o que não foi tão legal e poderia melhorar), sugestões (tanto de tema quanto de melhorias para o dojo) e o registro dos participantes.

Outro costume importante é disponibilizar o que foi produzido publicamente para consulta dos participantes e para a comunidade como um todo.

## Ferramentas

Para auxiliar na experiência do dojo existem diversas ferramentas, a seguir apresentarei algumas das que eu utilizo:

### Editor de Texto

Eu sou um usuário de VIM e adoro utiliza-lo. Porém, a não ser que seja um dojo de VIM não é uma ferramenta muito recomendada pois pode causar frustração nas pessoas que não o utilizam.

No dojo eu gosto de utilizar o [sublime text](https://www.sublimetext.com/), por ser direto ao ponto e oferecer o code highlighting sem ter que configurar nada. Mas caso não possua a licença do sublime exitem outros bons editores gratuitos como o [Atom](https://atom.io/).

### Biblioteca de Testes

Na realização dos testes costumo utilizar a biblioteca padrão de testes do Python, a [unittest](https://docs.python.org/3/library/unittest.html) em conjunto com a biblioteca [nose](https://nose.readthedocs.io/en/latest/) para facilitar o processo de rodar os testes.

Para escrever testes com a **unittest** basta criar uma classe que extende `unittest.TestCase`:

```python
import unittest

def incremento(x):
    return x + 1

class MeuTeste(unittest.TestCase):
    def testa_incremento(self):
        self.assertEqual(incremento(3), 4)
```

O exemplo acima implementa um método em `MeuTeste` que verifica se a função `incremento` com o parâmetro `3` retorna o valor esperado `4`.

Rodando os testes com o **nose**:

```console
$ nosetests arquivo.py
```

Ele buscará em seu arquivo todas classes que extendem `unittest.TestCase` e rodar os testes automaticamente.

### Semáforo de Testes

Para saber se os testes estão passando teríamos que ficar rodando o comando de teste em toda alteração no código. Com um semáforo automatizamos este processo além de rodar os testes sempre que o arquivo é salvo disponibilizando um feedback visual bem interessante.

O semáforo que eu gosto de utilizar é o [dose](https://github.com/danilobellini/dose) do [Danilo Bellini](https://twitter.com/danilobellini):

![python dose semáforo de testes para coding dojo](assets/dose.png)

Ele funciona tanto com python quanto com outras linguagens além de integrar bem com o **nose**, basta rodar o comando:

```console
$ dose nosetests arquivo.py
```

{{< tip class="warning" >}}
Caso tenha problemas no processo de instalação dê uma olhada na postagem que eu fiz sobre [dependências dev python](/pt-br/blog/tldr-dependencias-dev-python-no-ubuntu/), talvez ela possa lhe ajudar nesta questão.
{{< /tip >}}

## Concluindo

No dojo o mais importante é se divertir e aprender, estas são apenas algumas dicas a partir da minha experiência. Sintam-se a vontade para adaptar, criar e subverter tudo que foi descrito aqui e poste o que funcionou e o que não deu muito certo nos comentários.

Para quem estiver curioso sobre os códigos produzidos no dojo ou quiser sugestão de temas dê uma olhada no [repositório do Grupy-DF](https://github.com/grupydf/dojos) que é onde eu e a comunidade Python do Distrito Federal mantemos nosso registro dos dojos passados.

E para quem é da região do DF e ainda não conhece nossa comunidade, estão todos convidados a participar. O site com todos os contatos da comunidade é [df.python.org.br](http://df.python.org.br/).

Uma indicação do [@juanplopes](https://twitter.com/juanplopes) nos comentários da postagem em inglês foi o [repo do DojoRio](https://github.com/dojorio/dojo-centro) que já vem fazendo semanalmente nos últimos 10 anos dojos e tem em seu repositório mais de 400 dojos salvos.

Outra indicação do [@juanplopes](https://twitter.com/juanplopes) é o [semáforo DojoTimer](https://github.com/juanplopes/dojotimer) que além de rodar os testes também gerencia o tempo do dojo e salva os nomes dos pilotos. É uma ferramenta muito legal que com certeza será usada no meu próximo dojo e assim que usar voltarei aqui para escrever uma seção para ele.
