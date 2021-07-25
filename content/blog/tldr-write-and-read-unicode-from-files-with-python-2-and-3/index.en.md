---
title: "TLDR: Write and Read Unicode from files with Python 2 and 3"
description: "Read and write Unicode characters on files keeping the consistency of code between Python 2 and Python 3 codebases"
publishDate: 2018-08-23
tags:
  - tldr
  - python
  - unicode
images:
  - /img/tldr/encode-decode.png

---

Chatting with a friend, [Mário Sérgio](https://twitter.com/queirozMario21), about problems that happen when you migrate your codebase from Python versions, I came out with the idea of writing this **TLDR**.

I hope it can help someone that is trying to work with texts that contain [Unicode](https://en.wikipedia.org/wiki/Unicode) characters that don't fall back into the [ASCII](https://en.wikipedia.org/wiki/ASCII) table like other than the roman alphabet and [emoji](https://en.wikipedia.org/wiki/Emoji).

On Python 2 there's no distinction between **byte** and **string**. It leads us to eventually not correctly encode/decode the data when we deal with input and output. That kind of mistake can cause runtime errors like that:

{{< highlight console >}}
UnicodeDecodeError: 'ascii' codec can't decode byte 0xc3 in position 1: ordinal not in range(128)
{{< / highlight >}}

Changing to Python 3, where **byte** and **string** are distinct types, the code in most cases stop working or presents weird behaviors like mixing text with their textual representation of the codification:

{{< highlight python >}}
'ol\xc3\xa1'
{{< / highlight >}}

The solution to write a code that runs across both versions is to follow the [zen of python](https://www.python.org/dev/peps/pep-0020/) where it says that "Explicit is better than implicit" and always define what is **byte** from what is **string**:

![encode decode](/img/tldr/encode-decode.png)

The following code opens a temporary file to write in **byte**  mode and writes the content of a **string** that was encoded into **byte** using the [utf-8](https://en.wikipedia.org/wiki/UTF-8) encoding. Then, the inverse process is done, opening the file for reading in **byte** and decoded to **string** to print to the terminal:

{{< highlight python >}}
# -*- coding: utf-8 -*-

from __future__ import print_function, unicode_literals

import tempfile

unicode_file = tempfile.mktemp()

# escrita
with open(unicode_file, 'wb') as f:
    f.write('こんにちは'.encode('utf-8'))

# leitura
with open(unicode_file, 'rb') as f:
    print(f.read().decode('utf-8'))
{{< / highlight >}}

The result is a code that runs in Python 2 and also in Python 3:

![running the code](/img/tldr/unicode-file.png)

{{< tip class="info" >}}
This tip works as well for another encoding type like `latin-1`.
{{< /tip >}}
