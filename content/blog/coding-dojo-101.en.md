---
title: Coding Dojo 101
description: Discover what is a Coding Dojo and how to start it at you college or at you local community
publishDate: 2016-12-02
images:
  - /img/coding-dojo-101/flow-en.jpg
tags: ["dojo", "learning", "python"]
aliases: [
  "/en/blog/coding-dojo-101"
]
draft: false

---

My first **Coding Dojo** happened right after my first contact with the **Python** language at college. A classmate ([Dirley](https://github.com/ravishi)) that had back from **Python Brasil** conference where he discovered the concept of Coding Dojo and was excited to put that to practice. That was a really fun day and probably the most important moment that started a spark on me to learn more about Python.

In the same way as that brought me to Python and after to the community, Coding Dojo can be an entry door to a lot of people to programming.

### What is Coding Dojo?

Just like on martial arts the dojo is an environment for learning and this concept must be reinforced to everyone that are participating on it.

Besides that it's an environment where we apply the concepts of **Test Driven Development** (e.g. **TDD**), **Pair Programming** and **baby steps** to solve a challenge given by the dojo master.

The challenge resolution isn't an obligation to call it as a successful dojo, the main goal is to be a fun experience to everyone.

This video illustrates briefly how it works (its in Portuguese, but you can turn on the caption auto translation):

{{< youtube vqnwQ3oVM1M >}}

A dojo can be done remotely, but the face-to-face one is where the magic happens.

### Requirements

To make a dojo you don't need much:

- A room or place with some seats
- Only one computer where people will code
- A big screen or projector where the crowd can see the coding

### The Coding Dojo Flow

The flow of a coding dojo is pretty much like that:

![the flow of a coding dojo](/img/coding-dojo-101/flow-en.jpg)

It has **cycles of 5 to 7 minutes**, which can be changed, but for beginners it's recommended that the cycle don't be too big to avoid someone getting stuck at some logic and also to invite more people to go to the computer to code (I personally like it to be of 5 minutes).

The **pilot** is the only one who codes and can't be interrupted by the audience during a cycle, where the **pair programming** is happening with the **co-pilot**, which is the only person allowed to give opinions about the code.

The **audience** remains quiet until the end of each cycle. Some people like to make some quick pauses when the **tests are passing** to discuss something with the audience which is fine, but when the **tests are failing** any interaction of the audience with the pilot and co-pilot **is not allowed**.

On the break of each cycle both pilot and co-pilot share its toughs of what they are doing with the audience, the pilot goes back to audience, the co-pilot goes to the pilot seat and someone from the audience (preferably who not coded yet)  assumes voluntarily the co-pilot seat.

That will repeat all along the dojo until the challenge being solved or the dojo time is up (something around 40 minutes to one hour and a half).

### Pair Programming

**Pair Programming** is a technique used at the dojo and at programming environments to improve team work and share knowledge. It is basically two people working together at a computer to solve a problem by talking about it giving opinions on the implementation, helping to remember how to do something and collaborating as team towards the solution.

### Test Driven Development

Another essential technique used on a coding dojo is **Test Driven Development** (e.g. **TDD**). It is about writing tests before the real implementation, you write tests that proves your business logic first, then you write code to make it pass.

When the tests passes it's time to write more tests that covers another aspect of the business logic and so forth.

### Babysteps

To write code using **TDD** can be a real challenge for beginners, people try to test the hole business logic at first to comeback to the "real coding".  The problem here is that you get an awful  first impression about **TDD** getting stuck for hours at complex tests.

That's why at a dojo we use another technique called **babysteps** where you break your problem at smaller pieces as possible and start to write tests for this tiny bits of logic until reach the big picture. It's easier to your co-pilot and the audience to understand where you are trying to go with the code besides, you only have 5 to 7 minutes to the next pilot assume the control.

### Finishing the Coding Dojo

At the end of e dojo it's a good practice to get all **pros** and **cons** about that experience and also to get suggestions about what can be done to improve the next dojo session.

All the participants gets their names at the dojo list and the code and the feedback are posted in a public repo to inspire others to make their own dojos.

## Tools used in a Coding Dojo

To help improve the dojo experience there are some tools that I like to use:

### Text editor

I am a VIM user and I love to use it but, that is not a good tool to present a dojo to beginners, unless you are making a VIM dojo.

So at the dojo I prefer to use beginner friendly text editors like [Sublime Text](https://www.sublimetext.com/) or [VS Code](https://code.visualstudio.com/) that have code highlighting and autocomplete support without much tweaking.

### Test Library

On a Python dojo I like to use for tests Python's standard library [unittest](https://docs.python.org/3/library/unittest.html) in conjunction with [nose](https://nose.readthedocs.io/en/latest/) test runner to keep configuration as low as possible and to use Python's batteries included feature.

To write a test with **unittest** you must create a class that extends `unittest.TestCase`:

{{< highlight python >}}
import unittest

def increment(x):
    return x + 1

class MyTest(unittest.TestCase):
    def test_increment(self):
        self.assertEqual(increment(3), 4)
{{< / highlight >}}

The example above creates a test that tests a function called `increment` checking if we call the function passing `3` as parameter we will receive `4` as result.

To run de tests with **nose** we call:

{{< highlight console >}}
$ nosetests file.py
{{< / highlight >}}

It will look into the file for classes that extends `unittest.TestCase` and will run all of its methods that have `test` prefix automatically.

### Semaphore

To keep everyone aware about if the tests are passing we will need to run the test command at each change to the code but, we can improve that and run the tests automatically and with a good visual feedback using a tests semaphore called [dose](https://github.com/danilobellini/dose) created by [Danilo Bellini](https://twitter.com/danilobellini):

![dose coding dojo semaphore](/img/coding-dojo-101/dose.png)

It works with python and another languages too.

To run our tests with **nose** just call:

{{< highlight console >}}
$ dose nosetests file.py
{{< / highlight >}}

{{< tip class="warning" >}}
If you have any problem with the installation process check out this post that I made about [Python dev dependencies](/blog/tldr-python-dev-dependencies-on-ubuntu/).
{{< /tip >}}

## Conclusion

At the dojo the most important thing is to learn and have fun learning, this was just some tips by my experience at Coding Dojos. Feel free to adapt, create and subvert everything that was written here and go back to tell me what was your experience, things that worked, things that not much and so forth at the comments bellow.

If you want some reference of dojo I have some but they are in Portuguese but with some help with google translator you can get along:

- My local community [dojo github repo](https://github.com/grupydf/dojos) where you can find some challenges, how it was solved at the dojo and how its projects looks like.
- The [Dojo Puzzle website](http://dojopuzzles.com/) where you can get random dojo challenges.
- An indication of [@juanplopes](https://twitter.com/juanplopes) at the comments is the [DojoRio github repo](https://github.com/dojorio/dojo-centro), they have about 10 years of history on making weekly dojos and currently have more than 400 dojo sessions on their repo.
- Another one from [@juanplopes](https://twitter.com/juanplopes) is the [DojoTimer semaphore](https://github.com/juanplopes/dojotimer) that runs tests and also keeps session time and logs the pilots names, that is an awesome tool and i will definitely use at my next dojo and comeback here to and a section for it.
