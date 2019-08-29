---
title: Exploring pygame 2 -  Drawing on Screen
description: Learn how drawing works at pygame and how to write your first drawing codes
publishDate: 2017-02-03
images:
  - /img/exploring-pygame/drawing-axis.jpg
tags: ["python", "pygame", "gamedev"]
draft: false

---

During a game lifetime, we are constantly drawing on the screen. Now that we learned how to create a program with pygame it's time to start to draw.

## Drawing axis

Going back to math classes at high school we were introduced to the [Cartesian coordinate system](https://en.wikipedia.org/wiki/Cartesian_coordinate_system). It is basically one bidimensional plane oriented by the axis **x** and **y** where the **x** values grow from left to right while **y** grows from bottom to top:

![cartesian axis](/img/exploring-pygame/cartesian-axis.jpg)

At pygame there's a little change, the drawing space will use the coordinates **x** and **y** too but the **y** axis will follow the opposite direction from the cartesian coordinate system, **y** will grow from top to bottom and the visible area of the screen will be located starting from the point `x=0` and `y=0` as the top left corner of your program window going until the size defined at the command `pygame.display.set_mode((x, y))`:

![drawing axis](/img/exploring-pygame/drawing-axis.jpg)

## Let's Draw!

Start by creating a file `draw.py` with the following content:

{{< highlight python "linenos=table" >}}
import time

import pygame

# defining the colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

pygame.init()

screen = pygame.display.set_mode((640, 480))
# loading the font to be used at drawing time
font = pygame.font.SysFont(None, 55)

pygame.display.set_caption('Drawing')

screen.fill(BLACK)

# drawing at the surface
pygame.draw.line(screen, WHITE, [10, 100], [630, 100], 5)
pygame.draw.rect(screen, BLUE, [200, 210, 40, 20])
pygame.draw.ellipse(screen, RED, [300, 200, 40, 40])
pygame.draw.polygon(screen, GREEN, [[420, 200], [440, 240], [400, 240]])

pygame.display.flip()

time.sleep(5)

screen.fill(BLACK)

# writing pygame at the buffer
text = font.render('pygame', True, WHITE)
# coping the text to the screen
screen.blit(text, [250, 200])

pygame.display.flip()

time.sleep(5)
{{< / highlight >}}

The program starts by defining the values of the colors that will be used later. Following it initializes pygame like at the previous post and then it loads the font at `pygame.font.SysFont` to be ready to use later.

After filling the `screen` surface with the black color some of the drawing functions available at `pygame.draw` are used:

- **line:** draws a line receiving as parameters the surface following by its color, a list with the starting and the end coordinates, and the line thickness in pixels.
- **rect:** draws a rectangle receiving as parameters the surface following by its color and a list with the starting x position, starting y position, width, and height.
- **ellipse:** draws a circumference receiving as parameters the surface following by its color and a list with the same structure of **rect**.
- **polygon:** draws a polygon receiving as parameters the surface following by its color and a list with the coordinates from the polygon vertices.

After that `pygame.display.flip()` refreshes the entire drawing area of the surface `screen`, following by an interval of 5 seconds.

After the `sleep` it fills the surface with black and the text that will be written is defined using the `font.render` method where the first parameter is the `string` pygame, then the value `True` that activate the anti-alias that smooths the text, and as third parameter white as the color of the font. This method creates an intermediary surface with the text that will be passed to the `screen` surface with the method `blit` that receives as parameter the surface that will be copied and its position at the surface `screen`.

And at the end, the screen refresh is called followed by a 5 seconds sleep and the program is done.

## Conclusion

There are many ways to draw on the screen with pygame, you can see the other methods with details from the [library documentation](https://www.pygame.org/docs/ref/draw.html).

The code of this chapter can be found at the repo [exploring-pygame](https://github.com/humrochagf/exploring-pygame/blob/master/02-drawing).
