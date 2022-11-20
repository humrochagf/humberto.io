---
title: "Pull from multiple repositories"
publishDate: 2022-05-23
tags:
  - git

---

When you have a list of child folders and need to pull latest from all of them:

```shell
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && git pull && cd .." \;
```

The `\( ! -name . \)` pattern is used to skip executing the command inside the `.` folder.
