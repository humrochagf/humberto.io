---
title: "Publishing my first Game"
description: "A brief tale about my journey of publishing my first game"
publishDate: 2019-08-28
tags:
  - python
  - pyxel
  - gamedev
images:
  - /img/first-game/pyxel-lander-splash-screen.png

---

Games always connected me with technology since the beginning.

My father and I, we built our first computer (a Pentium 286) and the first thing that I remember to do was to play some DOS games like Prince of Persia and Lunar Lander. I learned a bunch of CLI commands just to play my favorite games.

The passion for playing and making games followed me as a hobby. I have a [pygame series of posts](https://humberto.io/tags/pygame/) on this blog, where I go through basic concepts of game development trying to explain them to someone who is starting to learn about it.

Time has passed, the day to day hush started to take most of my energy and my pygame series hasn't seen a new post for a while, so as my hobby.

Then, last December during the holidays, I discovered [Pyxel](https://github.com/kitao/pyxel). It is a Python retro game engine that follows the [pico-8](https://en.wikipedia.org/wiki/Pico-8) limitations and comes with a sprite, tilemap, and music editor.

I love pixel art games, so I decided to take the holidays to write my first complete game from the ground up until its publication. The game is a tribute to the old Lunar Lander DOS game that I played at my childhood.

{{< videogif "/img/first-game/pyxel-lander-landing.webm" >}}

The theme choice was meaningful to me, not only because I played when I was little, but also because I've coded it with a friend at one of my [projects](https://github.com/ravishi/lunar-lander-ex/commits/master) at my Computer Science course.

The process of building the game was quite fun, and at the end of the day, I got the lunar module flying on the screen. The day after I dived into the procedural moon surface building, and then when I was about to figure out the collision detection my holiday time was over, the day to day rush came back and my project stale.

More time passed, I forgot about that project, then, during a lunch break, scrolling on Twitter I saw the following question:

> "When was the last time you coded something just for fun?"

I love coding and blogging, and most of the time I have fun on my day to day doing it, but something 100% for fun reminded me that Christmas codding day.

I've had uncountable RPG Maker unfinished games when I was young, and many others with different tools. What is so different from my work, or from my Open Source projects that makes me finish then, but not my Game projects?

I decided to finish the game to understand what was holding me back and... I've got the game finished, and published \\o/. You can check it out and play it at:

{{< itchio 471797 >}}

Also, I released it as Open Source, and anyone interested to see the code can go to https://github.com/humrochagf/pyxel-lander

## Wrapping Up

Going through this whole process made me realize somethings:

* Game development can be like a hydra. While you are solving one thing, your mind is creating new features on the go. So, like any other field of software development, focus on the [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product), and keep new features to the next releases or projects.
* If it's your first game, try to not over complicate things, it's more important to go through the whole experience from the idea to the publishing, instead of making the perfect game.
* Tools with clear and limited scope like Pyxel are great to make your first game. It helps you a lot to keep it simple.
* Call your friends when you need some help. Thanks [Jairo Jair](https://jairojair.com/) for the help with the MacOS build.
* The coding just for fun thing is fantastic to learn new things and have fun in the process, let's do more of those.
