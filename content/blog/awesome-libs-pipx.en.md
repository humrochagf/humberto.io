+++
draft = true
linkTitle = ""
title = "Awesome Libs: pipx"

+++
One of the main perks of a good Developer is having a proper toolset available on your belt.

This series of posts gives you tips on libraries that can be handy on your day to dar as a Developer and new stuff that you should keep an eye on.

So our first lib is handy for you that loose count how many times you installed some python tool into a Python virtual environment.

Pipx is a lib install your Python programs into isolated environments, so you can use them as a command line program without messing with your  system  environment and also without  having to enter on virtualenvs to run a cli command.

{{< highlight python >}}
python3 -m pip install --user pipx
{{< / highlight >}}

{{< highlight python >}}
python3 -m pipx ensurepath
{{< / highlight >}}