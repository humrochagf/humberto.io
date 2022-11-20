---
title: "Empty commit"
publishDate: 2022-02-18
tags:
  - git

---

Sometimes you just need to make an empty commit to trigger a CI pipeline or a webhook.

```shell
git commit --allow-empty -m "Trigger CI"
```
