---
title: "Setup ssh keys for remote access"
publishDate: 2026-03-23
tags:
  - ssh
  - public-key
  - remote-access
  - self-hosting
---

To easily setup your public keys into freshly created servers, specially when copy and paste is limited like in proxmox web console you can benefit for one of your accounts in a public or private git servers reachable by your server like Forgejo, Github, and Gitlab.

They all have the same API where you can fetch your public keys under the pattern:

```text
https://<git-server>/<username>.keys
```

Try it in your browser to see the output.

So, all you have to do is run this command in your server session replacing the `git-server` address and the `username` to match yours:

```sh
curl https://<git-server>/<username>.keys >> ~/.ssh/authorized_keys
```

That will append your public keys into the authorized keys of the logged account  and next time you ssh into that `account@server` it will use your ssh key as credentials for the connection. 
