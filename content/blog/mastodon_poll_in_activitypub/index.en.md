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

Recently, I started to run my own [ActivityPub](https://activitypub.rocks/) instance with [Takahē](https://jointakahe.org/) and also to contribute to the project.

While contributing to the implementation of a Mastodon compatible version of the Poll functionality, I noticed that the information on how does the services perform the poll related operations is kind of scattered through multiple places.

For me to remember it and maybe to help someone looking for the same information I made this article to summarize things a bit.

{{< tip class="info" >}}
These are my findings while first implementing the feature. It may contain incomplete or not so precise information about how things really work in Mastodon side.

If you find something that needs correction, feel free to send me a toot at `@humrochagf@humberto.io` from your ActivityPub account 🙂
{{< /tip >}}

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

## Poll in ActivityPub

In ActivityPub side the poll is represented by the core type `Question` that is defined at [the Activity Types docs](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-question) with a `toot` extension that adds `votersCount` to compose the type and you can find it's definition at [the Mastodon ActivityPub docs](https://docs.joinmastodon.org/spec/activitypub/#poll-specific-properties).

Looking it's representation it should look like this:

```json
{
   "@context":[
      "https://www.w3.org/ns/activitystreams",
      {
         "Emoji":"toot:Emoji",
         "Hashtag":"as:Hashtag",
         "Public":"as:Public",
         "sensitive":"as:sensitive",
         "toot":"http://joinmastodon.org/ns#",
         "votersCount":"toot:votersCount"
      },
      "https://w3id.org/security/v1"
   ],
   "id":"https://ap.example.com/users/postcreator/statuses/1234",
   "type":"Question",
   "votersCount":10,
   "attachment":[
   ],
   "attributedTo":"https://ap.example.com/users/postcreator",
   "cc":"https://ap.example.com/users/postcreator/followers",
   "closed":"2023-01-01T20:04:45Z",
   "content":"<p>Example Poll</p>",
   "contentMap":{
      "en":"<p>Example Poll</p>"
   },
   "endTime":"2023-01-01T20:04:45Z",
   "oneOf":[
      {
         "type":"Note",
         "name":"Option 1",
         "replies":{
            "type":"Collection",
            "totalItems":6
         }
      },
      {
         "type":"Note",
         "name":"Option 2",
         "replies":{
            "type":"Collection",
            "totalItems":4
         }
      }
   ],
   "published":"2023-01-01T01:00:00Z",
   "replies":{
      "id":"https://ap.example.com/users/postcreator/statuses/1234/replies",
      "type":"Collection",
      "first":{
         "type":"CollectionPage",
         "items":[
         ],
         "next":"https://ap.example.com/users/postcreator/statuses/1234/replies?only_other_accounts=true&page=true",
         "partOf":"https://ap.example.com/users/postcreator/statuses/1234/replies"
      }
   },
   "sensitive":false,
   "tag":[
   ],
   "to":"as:Public",
   "url":"https://ap.example.com/@postcreator/1234"
}
```

In short Mastodon submits a **single-choice** poll by adding the choices in the `oneOf` attribute, and submits a **multiple-choice** poll by adding them to the `anyOf` attribute.

The `endTime` keeps the expiration time of the poll while the `closed` will be present only when the poll was closed and in general will have the same value of `endTime` in Mastodon, but in broader use it seems to be also used to close the poll earlier if needed.

Another point that worth mentioning is that Mastodon extension of `votersCount` becomes useful in the multiple-choice case scenario, where the sum of all votes can differ of the number of participants in the poll.

## Replying to a Poll

Looking further down to ActivityPub docs about [how to represent questions](https://www.w3.org/TR/activitystreams-vocabulary/#questions) the doc statement about how to reply to it is a bit vague:

> Responses to questions are expressed as **Objects** containing an **inReplyto** property referencing the **Question**.

Looking to the example JSON it's not clear what is the kind of activity used in this case, but inspecting Mastodon communication I found that the activity type used to represent it is `Create` targeting an object of type `Note` and it looks like this:

```json
{
   "id":"https://anotherinstance.com/users/postvoter#votes/389574/activity",
   "to":"https://ap.example.com/@postcreator@ap.example.com/",
   "type":"Create",
   "actor":"https://anotherinstance.com/users/postvoter",
   "object":{
      "id":"https://anotherinstance.com/users/postvoter#votes/389574",
      "to":"https://ap.example.com/@postcreator@ap.example.com/",
      "name":"Option 1",
      "type":"Note",
      "inReplyTo":"https://ap.example.com/@postcreator@ap.example.com/posts/145144397271001760/",
      "attributedTo":"https://anotherinstance.com/users/postvoter"
   },
   "@context":[
      "https://www.w3.org/ns/activitystreams",
      {
         "toot":"http://joinmastodon.org/ns#",
         "Emoji":"toot:Emoji",
         "Public":"as:Public",
         "Hashtag":"as:Hashtag",
         "votersCount":"toot:votersCount",
      },
      "https://w3id.org/security/v1"
   ]
}
```

The attribute `inReplyto` points to the activity that contains the question/poll while the `name` matches the exact content of the question being voted.

Looking to a [discussion in ActivityPub discuss](https://socialhub.activitypub.rocks/t/votes-on-question-activities/2880) I found a nice description of the fields present in the object:

```text
id: ...
type: Note
name: <the exact text of the poll option>
inreplyTo: <the status that holds the poll>
attributedTo: <you>
to: <the poll creator>
```

Also, different from a normal post the poll answer doesn't have a content among its attributes.

## Conclusion

Operations around the ActivityPub Question type are not super clear, and Mastodon docs also don't fill all the blanks in it, but I hope with those info you can know a bit more about how things work behind the scenes 🙂
