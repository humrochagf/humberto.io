---
title: "TLDR: How to Publish to PyPI"
description: Publish your first package to the Python Package Index (PyPI)
publishDate: 2018-03-05
images:
  - /img/generics/pypi.png
tags: ["tldr", "python", "pypi"]
draft: false

---

Have you ever asked yourself how to publish your Python package, so you can install it using [pip](https://pypi.python.org/pypi/pip)? It is less complex than it seems, and anyone can do it.

The first step is to create an account on the [Python Package Index (PyPI)](https://pypi.python.org/pypi) were all Python packages are registered.

After that, you need to create a file called `setup.py` on the root folder of the Python code that you will publish with the following content, changing the values were is needed:

{{< highlight python >}}
#!/usr/bin/env python
# -*- coding: utf-8 -*-

from setuptools import find_packages, setup

setup(
    name='my-python-package',
    packages=find_packages(),
    version='1.0.0',
    description='Short description of your package',
    long_description='Long description of your package',
    author='Your name',
    author_email='my@email.com',
    url='https://github.com/user/my-python-package',
    install_requires=['dependency1', 'dependency2'],
    license='MIT',
    keywords=['dev', 'web'],
    classifiers=[
        'Intended Audience :: Developers',
        'License :: OSI Approved :: MIT License',
        'Natural Language :: English',
        'Programming Language :: Python :: 2',
        'Programming Language :: Python :: 3',
    ],
)
{{< / highlight >}}

The `find_packages` function is an excellent tool to discover the files that compose your package automatically. You can check its [docs right here](http://setuptools.readthedocs.io/en/latest/setuptools.html#using-find-packages).

The `classifiers` are used to help users to find your package by indexing it into several categories, and you can look to the complete list following this [link](https://pypi.python.org/pypi?%3Aaction=list_classifiers).

## Packaging

Before publishing your package, you need to package it first, and the easiest way to do that is running:

{{< highlight console >}}
$ python setup.py sdist
{{< / highlight >}}

{{< tip class="info" >}}
This packaging model creates a source distribution with everything that you need to build/compile your code at any platform. You will have a build phase every time you install it.
{{< /tip >}}

To package it with a pre-compiled code you need to run:

{{< highlight console >}}
$ python setup.py bdist_wheel
{{< / highlight >}}

{{< tip class="info" >}}
This command packages a built distribution for the architecture that you run the command. For example, if your code supports Python 2 and 3 you need to run it on both Python versions.
{{< /tip >}}

Done that, you will have everything packed up inside the folder `dist/`.

## Publishing

To publish your package, you will need to install [twine](https://github.com/pypa/twine). It is a tool to publish packages at the PyPI:

{{< highlight console >}}
$ pip install twine
{{< / highlight >}}

After installed run the following command:

{{< highlight console >}}
$ twine upload --username pypi-username dist/my-package-1.0.0.tar.gz dist/my-package-1.0.0-py2-none-any.whl dist/my-package-1.0.0-py3-none-any.whl
{{< / highlight >}}

{{< tip class="warning" >}}
Don't forget to replace the username and the file names with your username and file names.
{{< /tip >}}

The command will ask for your password, and then, it will upload it to the PyPI.

## Bonus - Versioning

A common practice is to keep the package version and another package information inside the `__init__.py` file like this:

{{< highlight python >}}
__author__ = 'My Name'
__email__ = 'my@email.com'
__version__ = '1.0.0'
{{< / highlight >}}

You can add the following code at the `setup.py` to use this info from the `__init__.py` at your package:

{{< highlight python >}}
import os
import re

package = 'my_package'

with open(os.path.join(package, '__init__.py'), 'rb') as f:
    init_py = f.read().decode('utf-8')

version = re.search(
    '^__version__ = [\'\"]([^\'\"]+)[\'\"]', init_py, re.MULTILINE
).group(1)
author = re.search(
    '^__author__ = [\'\"]([^\'\"]+)[\'\"]', init_py, re.MULTILINE
).group(1)
email = re.search(
    '^__email__ = [\'\"]([^\'\"]+)[\'\"]', init_py, re.MULTILINE
).group(1)

...
setup(
    ...
    version=version,
    author=author,
    author_email=email,
    ...
)
{{< / highlight >}}

## Bonus - README.md as long_description

If you use [github](https://github.com) you probably write a **readme** file to describe your package. You can use it as your `long_description`, so you don't need to repeat yourself:

{{< highlight python >}}
with open('README.md', 'rb') as f:
    readme = f.read().decode('utf-8')

setup(
    ...
    long_description=readme,
    long_description_content_type="text/markdown",
    ...
)
{{< / highlight >}}
