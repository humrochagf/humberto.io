---
title: "Pull from multiple repositories"
publishDate: 2022-05-23
tags:
  - git

---

A tip for when you have a list of git repositories to update:

```shell
find . -maxdepth 1 -type d \( ! -name . \) -exec bash -c "cd '{}' && git pull && cd .." \;
```

The `\( ! -name . \)` pattern is used to skip executing the command inside the `.` folder.
