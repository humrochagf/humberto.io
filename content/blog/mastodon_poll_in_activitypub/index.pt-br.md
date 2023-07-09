---
title: "Enquetes do Mastodon no ActivityPub"
description: "Um resumo de como as operações de enquete no Mastodon funcionam no ActivityPub"
publishDate: 2023-02-06
slug: enquetes-do-mastodon-no-activitypub
tags:
  - activitypub
  - mastodon
  - takahe
  - poll
  - question
images:
  - /img/posts/mastodon-activitypub.png

---

Eu comecei a rodar minha própria instância de [ActivityPub](https://activitypub.rocks/) usando o [Takahē](https://jointakahe.org/).

Me interessei em contribuir com o projeto e uma das primeiras funcionalidades que implementei foi o suporte a enquetes.

Enquanto eu implementava, notei que as informações sobre o assunto estavam espalhadas entre vários lugares diferentes.

Então, para que eu possa me lembrar do seu funcionamento, e para que talvez sirva de ajuda para alguém buscando pela mesma informação, eu decidi escrever este artigo.

## Enquete ou Pergunta? Como o ActivityPub lida com este conceito?

A primeira coisa que precisamos compreender é que o ActivityPub não suporta enquetes (Poll na definição do mastodon) como um tipo nativo em sua definição de protocolo.

O tipo mais próximo seria o tipo **Question** que é usado para representar qualquer tipo de pergunta, incluindo perguntas abertas que é uma informação importante sobre o assunto para ter em mente.

Olhando na [documentação do ActivityPub de como representar perguntas](https://www.w3.org/TR/activitystreams-vocabulary/#questions) podemos ver sua estrutura:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/1",
  "type": "Question",
  "content": "Qual é a resposta para a vida o universo e tudo mais?"
}
```

A pergunta acima é aberta, mas podemos ter perguntas de uma só resposta usando o atributo `oneOf`:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/2",
  "type": "Question",
  "content": "Qual é a resposta para a vida o universo e tudo mais?",
  "oneOf":[
    { "name":"123" },
    { "name":"42" },
    { "name":"666" }
  ]
}
```

Podemos também ter perguntas de múltipla resposta usando o atributo `anyOf`:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/3",
  "type": "Question",
  "content": "Qual é a sua estação favorita?",
  "anyOf":[
    { "name":"Primavera" },
    { "name":"Verão" },
    { "name":"Outono" },
    { "name":"Inverno" }
  ]
}
```

Quando não queremos parar de aceitar respostas para a pergunta, podemos indicar com o atributo `closed` passando um `DateTime` com o momento em que a pergunta foi fechada:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/4",
  "type": "Question",
  "content": "Corre antes que acabe, quem quer um pedaço de bolo?",
  "closed": "2023-01-01T00:00:00Z"
}
```

Estes são os atributos de base de uma pergunta no ActivityPub. Mais a frente veremos o que o Mastodon adicionou para fazer criar a enquete.

## Respondendo a uma Pergunta

A resposta para uma pergunta em ActivityPub é um pouco vaga. Ela não tem um tipo específico, mas requer a existência de alguns atributos:

```json
{
 "@context": "https://www.w3.org/ns/activitystreams",
 "attributedTo": "http://bob.example.org",
 "inReplyTo": "http://polls.example.org/question/2",
 "name": "42"
}
```

O atributo `attributedTo` indica quem está respondendo a pergunta, enquanto o atributo `inReplyTo` aponta para a URI da pergunta que estamos respondendo.

Embora não esteja mencionado na documentação, podemos inferir que o atributo `name` é a resposta para a pergunta e, em perguntas onde temos escolhas à selecionar, o texto de resposta deve ser exatamente igual ao texto de uma das opções informadas na pergunta.

Agora que sabemos tudo que o ActivityPub tem a dizer a respeito do assunto, vamos ver como o Mastodon decidiu implementar esta funcionalidade.

## A enquete no Mastodon

O Mastodon oferece alguns endpoints para lidar com enquetes:

- `GET /api/v1/polls/:id HTTP/1.1` para **recuperar** uma enquete ([docs](https://docs.joinmastodon.org/methods/polls/#get))
- `POST /api/v1/polls/:id/votes HTTP/1.1` para **votar** em uma enquete ([docs](https://docs.joinmastodon.org/methods/polls/#vote))
- `POST /api/v1/statuses HTTP/1.1` para **criar** uma enquete através da API de status ([docs](https://docs.joinmastodon.org/methods/statuses/#create))
- `GET /api/v1/statuses/:id HTTP/1.1` para **recuperar** uma enquete através da API de status ([docs](https://docs.joinmastodon.org/methods/statuses/#get))
- `DELETE /api/v1/statuses/:id HTTP/1.1` para **deletar** uma enquete através da API de status ([docs](https://docs.joinmastodon.org/methods/statuses/#delete))

No momento em que estou escrevendo este artigo, uma entidade que representa uma enquete no Mastodon tem este formato:

```json
{
  "id": "12345",
  "expires_at": "2023-01-01T23:04:45.000Z",
  "expired": true,
  "multiple": false,
  "votes_count": 10,
  "voters_count": null,
  "options": [
    {"title": "Opção 1", "votes_count": 6},
    {"title": "Opção 2", "votes_count": 4},
  ],
  "emojis": [],
  "voted": false,
  "own_votes": [],
}
```

O texto com a pergunta da enquete está localizado no atributo `content` do **status** da mesma forma em que um post normal estaria.

Você pode acessar [a documentation da entidade de enquetes](https://docs.joinmastodon.org/entities/Poll/) para saber mais a respeito.

## A enquete do Mastodon no ActivityPub

Voltando ao ActivityPub, uma enquete do Mastodon de fato usa o tipo `Question` oferecido pelo ActivityPub, mas ele também adiciona alguns atributos para poder representar corretamente a enquete que recuperamos através da API do Mastodon.

A [extensão `toot` do mastodon](https://docs.joinmastodon.org/spec/activitypub/#poll-specific-properties) é usada para adicionar o atributo `votersCount` à enquete.

```json
{
  "@context":[
    "https://www.w3.org/ns/activitystreams",
    {
      "toot":"http://joinmastodon.org/ns#",
      "votersCount":"toot:votersCount"
    },
    "https://w3id.org/security/v1"
  ],
  "id":"https://polls.example.org/users/sally/statuses/1234",
  "type":"Question",
  "votersCount":10,
  "attributedTo":"https://polls.example.org/users/sally",
  "closed":"2023-01-01T20:04:45Z",
  "content":"<p>Qual é o seu pokemon inicial favorito?</p>",
  "endTime":"2023-01-01T20:04:45Z",
  "oneOf":[
    {
      "type":"Note",
      "name":"Charmander",
      "replies":{
        "type":"Collection",
        "totalItems":5
      }
    },
    {
      "type":"Note",
      "name":"Bulbasauro",
      "replies":{
        "type":"Collection",
        "totalItems":2
      }
    },
    {
      "type":"Note",
      "name":"Squirtle",
      "replies":{
        "type":"Collection",
        "totalItems":3
      }
    }
  ],
  "published":"2023-01-01T01:00:00Z",
  "url":"https://polls.example.org/@sally/1234"
}
```

O atributo `endTime` é usado para adicionar um contador de tempo de expiração, enquanto `closed` está presente somente quando uma enquete foi fechada. Ambos quando preenchidos devem ter o mesmo valor.

A extensão do `votersCount` do Mastodon foi uma adição para dar suporte a questões de múltipla escolha, onde a soma dos votos pode ser diferente do número de participantes da enquete.

Também foi feita uma adaptação nas escolhas para contabilizar o número de votos por escolha:

```json
{
  "type":"Note",
  "name":"Charmander",
  "replies":{
    "type":"Collection",
    "totalItems":5
  }
}
```

A escolha foi transformada em um objeto do tipo `Note` que herda todos os atributos de `Object` criando a possibilidade de usar o atributo `replies` para guardar o total de votos de cada escolha.

## O voto do Mastodon no ActivityPub

Para votar em uma enquete o Mastodon usa o mesmo processo indicado no ActivityPub, porém com pequenas diferenças:

```json
{
   "id":"https://answer.example.org/users/bob#votes/389574/activity",
   "to":"https://polls.example.org/@sally@polls.example.org/",
   "type":"Create",
   "actor":"https://answer.example.org/users/bob",
   "object":{
      "id":"https://answer.example.org/users/bob#votes/389574",
      "to":"https://polls.example.org/@sally@polls.example.org/",
      "name":"Charmander",
      "type":"Note",
      "inReplyTo":"https://polls.example.org/@sally@polls.example.org/posts/1234/",
      "attributedTo":"https://answer.example.org/users/bob"
   },
   "@context":[
      "https://www.w3.org/ns/activitystreams",
      {
         "toot":"http://joinmastodon.org/ns#",
         "votersCount":"toot:votersCount",
      },
      "https://w3id.org/security/v1"
   ]
}
```

O tipo do objeto usado é `Note` para combinar com o tipo das opções de escolha.

Olhando para esta [conversa no forum do ActivityPub](https://socialhub.activitypub.rocks/t/votes-on-question-activities/2880) sobre a implementação do voto no Mastodon, temos este resumo sobre os atributos do objeto:

```text
id: ...
type: Note
name: <exatamente o mesmo texto da opção na enquete>
inreplyTo: <o status que contém a enquete>
attributedTo: <você>
to: <o criador da enquete>
```

Isto confirma que o Mastodon decidiu utilizar o que foi inferido sobre o atributo `name`.

Quando lidando com enquetes de múltipla escolha o Mastodon, no lugar de alterar o formato do objeto para enviar todas respostas de uma só vez, foi preferido enviar mais de uma mensagem de `Create` para a mesma enquete com cada uma das respostas selecionadas.

No final, para saber se a mensagem recebida é uma resposta para uma enquete, e não um post comum, é preciso verificar algumas coisas:

- O alvo do `inReplyTo` deve ser uma `Question`
- O tipo do objeto deve ser `Note` mas não possuí o atributo `content`
- A resposta está localizada no atributo `name`

## Conclusão

Operações que utilizam o tipo `Question` do ActivityPub não são muito claras quanto ao formato esperado e a documentação do Mastodon também não responde a todas as dúvidas deixadas pelo protocolo.

Entretanto espero que com estas informações você possa saber um pouco mais sobre seu funcionamento 🙂
