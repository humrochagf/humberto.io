---
title: "Matching linebreaks"
publishDate: 2022-02-20
tags:
  - vim

---

On vim search patterns while looking for linebreaks you have two different special characters:

- `\n` represents the null character byte `0x00`
- `\r` represents the carriage return byte `0x0a`

So on search/replace patterns to match a linebreak you should look for `\n` while when replacing you should use `\r` special character.

Example:

```regex
%s/\n/\r/g
```
