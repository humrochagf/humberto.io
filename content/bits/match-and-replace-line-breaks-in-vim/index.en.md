---
title: "Match and replace line breaks in vim"
publishDate: 2022-02-20
tags:
  - vim

---

There are 2 different special characters when dealing with line breaks:

- `\n` represents the new line character byte `0x0a`
- `\r` represents the carriage return byte `0x0d`

But in vim at the replacement side of the pattern things change a bit:

- `\n` becomes the null character `0x00`
- `\r` becomes the new line character `0x0a`

So at the search side you should use `\n` while at replace `\r`.

Example:

```text
%s/\n/\r/g
```
