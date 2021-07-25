---
title: "Exploring pygame 3 - Game Loop"
description: "Learn more about how the game loop works and how to implement it at pygame"
publishDate: 2017-08-24
tags:
  - python
  - pygame
  - gamedev
images:
  - /img/exploring-pygame/game-loop-en.png

---

Now that we learned how to draw on the screen ([previous post](/blog/exploring-pygame-2-drawing-on-screen/)) would be good that our game keeps running until someone closes it, and to do that we will use one of the basic concepts of game development with is the Game Loop.

## Concept

The concept of loop is something very common at computing, it is nothing but a sequence of actions and decision makings that repeats inside on a cycle. At a **low level**, we have the CPU that works in cycles of arithmetic operations managed by the **clock rate**, at **Operating Systems** there is a **loop** that manages the time that each process have with the CPU and that check for user inputs, and at **web servers** we use a **loop** to check if someone made a request to a page that will be processed and returned as response. With games, it shouldn't be different.

The loop used at game development is called **Game Loop** and is a key component at the structure of a game. On a simplified manner, we have the following structure:

![basic loop](/img/exploring-pygame/game-loop-en.png)

During the execution the game is always:

- **Input processing** that is done by detecting events created by input devices like a keyboard or a mouse.

- **Game update** with the events processed and some internal mechanics like game gravity, collision detection, and others the game variables are updated.

- **Drawing** the result of all this processing will be drawn on the screen to give visual feedback to the user.

Unlike an event loop at a desktop application where the program keeps waiting until you make an input, the game loop updates the game even if you don't provide any input because there are a lot of things happening that not require user input like music, physics, enemy A.I., and so forth.

Another important point that isn't present above is the time step control that keeps the game update rate smooth between different computers with different processing powers. But that is a topic for a future post.

## Implementation

To show the **game loop** running we will write a program that draws a ball that bounces through the screen:

{{< highlight python "linenos=table" >}}
import pygame

# define colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

pygame.init()

screen = pygame.display.set_mode((640, 480))

pygame.display.set_caption('Game Loop')

# the ball variables
position_x = 300
position_y = 200
velocity_x = 1
velocity_y = 1

# starting the game loop
while True:
    # INPUT PROCESSING

    # catch the events
    event = pygame.event.poll()
    # if the QUIT event is caught (when you click on the x to close the window)
    if event.type == pygame.QUIT:
        # end the loop and finish the program
        break

    # GAME UPDATE

    # change the ball position
    position_x += velocity_x
    position_y += velocity_y

    # change the x direction at the corners
    if position_x > 600:
        velocity_x = -1
    elif position_x < 0:
        velocity_x = 1

    # change the y direction at the corners
    if position_y > 440:
        velocity_y = -1
    elif position_y < 0:
        velocity_y = 1

    # DRAWING

    # fill the background with black
    screen.fill(BLACK)

    # draw the ball
    pygame.draw.ellipse(screen, RED, [position_x, position_y, 40, 40])

    # update the screen
    pygame.display.flip()
{{< / highlight >}}

The game loop is created with  `while True:` and will run indefinitely doing the game steps of input, processing and drawing at every cycle.

At the **input processing step** we use `pygame.event.poll()` to catch an event at the queue and check if it is an event to end the program. If it is we use `break` to end the loop and finish the program execution.

During the **game update step** we move the ball on the axis, x and y, checking if it collides with the corners and if that happens we change its direction.

And finally, at the **drawing step** we use the methods from the [previous post](/blog/exploring-pygame-2-drawing-on-screen/) to draw the ball on the screen.

{{< tip class="info" >}}
Note that we always fill the background with black before to draw the ball, if we don't do that, the ball will leave a trace on the screen from the previous draws.
{{< /tip >}}

## Conclusion

With that, we finish the brief ride into the **game loop**. Don't forget that all the code will be available at the repo [exploring-pygame](https://github.com/humrochagf/exploring-pygame/tree/master/03-game-loop).
