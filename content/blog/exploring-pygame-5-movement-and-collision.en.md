+++
description = "Learn how to move the objects on the screen and write you first collision detection with pygame"
draft = false
images = [
  "/img/exploring-pygame/movement-collision.png"
]
katex = true
publishDate = "2019-09-10T00:00:00-03:00"
tags = ["python", "pygame", "gamedev"]
title = "Exploring pygame 5 - Movement and Collision"

+++
Movement is part of a large portion of games. When jumping between platforms, shooting against a horde of enemies, piloting a space ship and running through the streets, we are causing movement and interacting with the game environment, applying action and causing reactions.

This chapter is to describe the basics of moving objects across the screen and their interaction with other elements through collision detection.

## Movement

If you are following this series of posts, you already saw an example of movement at the post about [the game loop](https://humberto.io/pt-br/blog/desbravando-o-pygame-3-game-loop/), where we implemented a ball that moves through the screen bouncing around.

This time we'll go through a similar code sample, adding more details to it and also, adding some new concepts:

{{< highlight python "linenos=table,hl_lines=21 26" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Simple Movement')

position_x = 0

while True:
    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # moves the square one pixel per cycle
    position_x += 1

    screen.fill(BLACK)

    # draws the square with the incremented position
    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

The code is pretty straight forward. We create a variable `position_x` to hold the square position on x.

Inside the loop, we increment its position by one pixel per cycle and then it's drawn again at its new position.

The drawing loop creates a cinematic effect of movement on the screen, but this implementation has a problem.

You can't control the object movement velocity, and powerful the computer, more loops are processed, causing results like that:

{{< videogif "/img/exploring-pygame/square-fast.webm" >}}

To fix this issue, we need to go back to physics class, when we learned about **linear motion**. To ensure constant velocity, we'll use the following formula:

$$d = d_{i} + v \Delta t$$

Applying it to the code, we'll have this:

{{< highlight python "linenos=table,hl_lines=16 19 24 26 28 36" >}}
import time

import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Velocity')

position_x = 0
# 100 pixels per second
velocity_x = 100

# capture the initial time
ti = time.time()

while True:
    # gets the time for
    # this cycle
    tf = time.time()
    # calculate the delta
    dt = (tf - ti)
    # sets final time as the initial time
    ti = tf

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # moves the square at the velocity defined
    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

We start defining the velocity at the x-axis to 100 pixels per second.

Then we get the initial time to be used at the delta time calculation.

Inside the loop, we capture the final time and at the next line, the `dt` (delta time) subtracting the final time by the initial time.

At **line 28**, the initial time receives the final time to be used at the next loop cycle.

In the end, we calculate the displacement at **line 36**.

{{< videogif "/img/exploring-pygame/square-velocity.webm" >}}

As we can see, it is possible to control the object movement velocity, but that solves only the visible part of the issue, the loop executes much more than the necessary generating noting more than overhead.

## FPS

These drawing cycles are also known as **frames**, and the frame rate control is a common practice among the audio-visual professionals. The measuring unit used on this kind of control is the **FPS** (**F**rames **P**er **S**econd).

Implementing this frame control gives you some benefits:

- Reduces de unnecessary machine overload
- Makes the multiplayer game sync easier to implement
- Reduces the [error propagation of the floating-point operation](https://floating-point-gui.de/errors/propagation/) (there are techniques to mitigate this kind of issue, but if you are starting, don't spend time on that yet)
- Increases the predictability and facilitates the process of planning your game. Now you can calculate how many processing you can have during a frame interval once you know the minimum requirements of your game.

The traditional refresh rate for movies is 24fps, and in games, it can change from 30 to 60fps. In our case, we'll use the 30fps refresh rate.

{{< highlight python "linenos=table,hl_lines=16 19 24" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('FPS')

position_x = 0
# since pygame clock returns its value
# in milliseconds, we divide the velocity
# by 1000 to keep the 100 pixel per seconds
velocity_x = 0.1

# create pygame clock
clock = pygame.time.Clock()

while True:
    # call the clock tick for 30 fps
    # and store the delta time
    dt = clock.tick(30)

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    position_x += velocity_x * dt

    screen.fill(BLACK)

    pygame.draw.rect(screen, WHITE, [position_x, 230, 20, 20])

    pygame.display.flip()
{{< / highlight >}}

Thanks to pygame's `Clock` implementation we could simplify things a little.

Different than Python's `time` library that works on a scale of seconds, pygame's `Clock` works on milliseconds, so we changed the velocity from `100` to `0.1` to ensure the same velocity of a hundred pixels per second.

Next, we instantiate `Clock` before entering the loop, and then, inside the loop, we call the `tick` function passing as argument the amount of **FPS** to limit the loop.

The `tick` function must be called on each cycle, so if a cycle runs faster than the expected rate, it sleeps for a brief moment to ensure the defined rate. As a result, the function returns the delta time between this loop and the previous one.

{{% tip class="info" %}}
Take a look at the [`tick` function documentation](https://www.pygame.org/docs/ref/time.html#pygame.time.Clock.tick). It has a difference of precision across platforms, but there is an alternate version of this function that is more precise (unfortunately uses much CPU), and you can use it if this level of precision is important to your game.
{{% /tip %}}

We have the square sliding through the screen on a constant speed, so let's proceed to the collision detection.

## Collision

The collision is the product of the interaction between the objects of your game. Their interaction can produce it, or the interaction with the environment.

The collision detection tends to grow on complexity as long as you add more elements with different formats to the scene.

At the example below, we'll see its basic concept by making the square interact with two pads, changing direction after colliding with them.

{{< highlight python "linenos=table,hl_lines=12-19 33-34 36-38 42-47" >}}
import pygame

BLACK = pygame.Color(0, 0, 0)
WHITE = pygame.Color(255, 255, 255)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Collision')

# create the square Rect
square = pygame.Rect(300, 230, 20, 20)

# create the pads Rect
left_pad = pygame.Rect(20, 210, 20, 60)
right_pad = pygame.Rect(600, 210, 20, 60)

pads = [left_pad, right_pad]

velocity_x = 0.1

clock = pygame.time.Clock()

while True:
    dt = clock.tick(30)

    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        break

    # use the move function inplace
    square.move_ip(velocity_x * dt, 0)

    # check for collision with the pads
    if square.collidelist(pads) >= 0:
        velocity_x = -velocity_x

    screen.fill(BLACK)

    # draw using the rect
    pygame.draw.rect(screen, WHITE, square)

    # draw the pads
    for pad in pads:
        pygame.draw.rect(screen, WHITE, pad)

    pygame.display.flip()
{{< / highlight >}}

The most straightforward collision detection consists of treating all elements as rectangular areas. Pygame has it implemented on the `Rect` class. It was used at the code to create a rectangular area to the square and for the pads.

With the creation of `Rect` we start to use the function `move_ip` to move it. This function changes the caller position in place, different from the `move` function that produces a new object with the position changed.

At **line 37** the `collidelist` function checks if any collision happened to return the collided object index from the list passed. In case there is no collision, it returns `-1`.

In the end, the square and the pads are drawn to the screen using their `Rect` instances to produce the following result:

{{< videogif "/img/exploring-pygame/square-collision.webm" >}}

## Conclusion

With these concepts of movement and collision, you can create some exciting games like [Pong](https://en.wikipedia.org/wiki/Pong). I'll end this post with a challenge to you. Use these concepts to implement the Pong game.

The code used to make this post is available at [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/05-movement-and-collision).
