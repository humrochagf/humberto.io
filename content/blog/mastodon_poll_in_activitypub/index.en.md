---
title: "Mastodon Poll in ActivityPub"
description: "A report on how does the Mastodon Poll operations work in ActivityPub"
publishDate: 2023-02-06
tags:
  - activitypub
  - mastodon
  - takahe
  - poll
  - question
images:
  - /img/posts/mastodon-activitypub.png

---

After starting to run my own [ActivityPub](https://activitypub.rocks/) instance with [Takahē](https://jointakahe.org/) I got interested in contributing to it. 

One of Takahē's features it to be compatible to Mastodon, so to add a feature we must to not only understand ActivityPub but also how Mastodon handles things in its side too.

While adding support to the Poll functionality, I noticed that the information about it is kind of scattered through multiple places.

For me to remember how it works, and maybe to help someone looking for the same information I decided to write this article.

{{< tip class="info" >}}
These are my findings while first implementing the feature. It may contain incomplete or not so precise information about how things really work in Mastodon side.

If you find something that needs correction, feel free to send me a toot at `@humrochagf@humberto.io` from your ActivityPub account 🙂
{{< /tip >}}

## Poll or Question? How ActivityPub handles it?

The first thing we need to know is that ActivityPub does not support Poll as a native type in it's protocol definition.

The closest type that we get is **Question**, that is used to represent any kind of question, including open-ended question and that is an important information to have in mind.

Looking in the [ActivityPub documentation on how to represent questions](https://www.w3.org/TR/activitystreams-vocabulary/#questions) we can see right the way how to represent one:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/1",
  "type": "Question",
  "content": "What is the answer to life the universe and everything?"
}
```

The question above is an open-ended question, but you can have a single-choice question using the `oneOf` property:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/2",
  "type": "Question",
  "content": "What is the answer to life the universe and everything?",
  "oneOf":[
    { "name":"123" },
    { "name":"42" },
    { "name":"666" }
  ]
}
```

You can also have a multiple-choice question using the `anyOf` property:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/3",
  "type": "Question",
  "content": "Which are your favorite seasons?",
  "anyOf":[
    { "name":"Spring" },
    { "name":"Summer" },
    { "name":"Autumn" },
    { "name":"Winter" }
  ]
}
```

When you don't want to receive any more answer you can indicate it by closing your question with the `closed` property indicating the `DateTime` of when it was closed:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "http://polls.example.org/question/4",
  "type": "Question",
  "content": "Fast before it's too late, who wants a slice of cake?",
  "closed": "2023-01-01T00:00:00Z"
}
```

Those are the base properties of an ActivityPub question. Further down this article we'll be able to see what Mastodon adds to it.

## Answering to a Question

The answer to a question in ActivityPub is a bit vague. It doesn't have an specific type, but rather required properties:

```json
{
 "@context": "https://www.w3.org/ns/activitystreams",
 "attributedTo": "http://bob.example.org",
 "inReplyTo": "http://polls.example.org/question/2",
 "name": "42"
}
```

The property `attributedTo` indicates who is answering to the question, while the property `inReplyTo` points to the URI of the question you are answering to.

Although it's not mentioned in the documentation we can infer that the `name` property is the answer to the question and in questions where we have choices to select. The answer text must be the same from one of the given choices.

Now that we know what ActivityPub has to say about it, we need to take a look on how Mastodon decided to implement it.

## The poll in Mastodon

Mastodon offers some endpoints to deal with polls:

- `GET /api/v1/polls/:id HTTP/1.1` to **retrieve** a poll ([docs](https://docs.joinmastodon.org/methods/polls/#get))
- `POST /api/v1/polls/:id/votes HTTP/1.1` to **vote** on a poll ([docs](https://docs.joinmastodon.org/methods/polls/#vote))
- `POST /api/v1/statuses HTTP/1.1` to **create** a poll through the status API ([docs](https://docs.joinmastodon.org/methods/statuses/#create))
- `GET /api/v1/statuses/:id HTTP/1.1` to **retrieve** a poll through the status API ([docs](https://docs.joinmastodon.org/methods/statuses/#get))
- `DELETE /api/v1/statuses/:id HTTP/1.1` to **delete** a poll through the status API ([docs](https://docs.joinmastodon.org/methods/statuses/#delete))

At the moment I'm writing this article an entity that represents a poll in Mastodon looks like this:

```json
{
  "id": "12345",
  "expires_at": "2023-01-01T23:04:45.000Z",
  "expired": true,
  "multiple": false,
  "votes_count": 10,
  "voters_count": null,
  "options": [
    {"title": "Option 1", "votes_count": 6},
    {"title": "Option 2", "votes_count": 4},
  ],
  "emojis": [],
  "voted": false,
  "own_votes": [],
}
```

The actual text asking the question to be answered by the poll is located at the `content` field of the **status** the same way a common post should be.

You can check [the poll entity documentation](https://docs.joinmastodon.org/entities/Poll/) to know more about it.

## Mastodon Poll in ActivityPub

Going back to ActivityPub the Mastodon poll indeed uses type `Question` provided by ActivityPub, but it also adds some other fields to be able to property represent the object that we get in Mastodon API. The `toot` [Mastodon extension](https://docs.joinmastodon.org/spec/activitypub/#poll-specific-properties) is also used to add the `votersCount` property.

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
  "content":"<p>What is your favorite starter pokemon?</p>",
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
      "name":"Bulbasaur",
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

The `endTime` is used to add an expiration time counter to he poll while the `closed` will be present only when the poll was closed. Both should have the same value.

The Mastodon extension of `votersCount` was an addition that is useful in the multiple-choice case, where the sum of votes can be different of the number of participants in the poll.

It also made an adaptation to the choices in order to keep track of the number of votes per choice:

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

The choices got transformed into objects of `Note` type that inherit all properties of `Object` so it was possible to use the `replies` property to store the total of votes a choice got.

## Mastodon vote in ActivityPub

To cast a vote in a poll, Mastodon uses the flow indicated in ActivityPub, but with a small difference:

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

The type of the created object is `Note` to match the type of the options.

Looking to a [discussion in ActivityPub forum](https://socialhub.activitypub.rocks/t/votes-on-question-activities/2880) about Mastodon implementation of a vote we have a summary about the object's fields:

```text
id: ...
type: Note
name: <the exact text of the poll option>
inreplyTo: <the status that holds the poll>
attributedTo: <you>
to: <the poll creator>
```

This confirms the that Mastodon decided to go with our inference about the `name` property.

When dealing with multiple-choice polls, Mastodon didn't changed anything in the object format, instead they just send more than one `Create` message from the same sender targeting the same poll, but with an answer to another choice.

So at the end, to be sure that the message you got is an answer to a poll and not a regular post or reply you need to check some things:

- The target to the `inReplyTo` property must be a `Question` 
- The type of the object is `Note` but with no `content` property
- The answer is located in the `name` property

## Conclusion

Operations around the ActivityPub Question type are not super clear, and Mastodon docs also don't fill all the blanks in it, but I hope with those info you can know a bit more about how things work behind the scenes 🙂
