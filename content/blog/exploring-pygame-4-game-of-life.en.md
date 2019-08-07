---
title: Exploring pygame 4 - Game of Life
description: Learn how to create a game of life implementation made for coding dojos
publishDate: 2017-08-27
images:
  - /img/exploring-pygame/rule-one.png
tags: ["python", "pygame", "gamedev"]
draft: false

---

A while ago, I made a post on [how to organize a coding dojo](/blog/en/coding-dojo-101/) that was published right after the Python DF community anniversary dojo where I prepared a challenge to create the [game of life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life) from John Horton Conway.

To let the challenge more dynamic, I prepared a graphic simulator using pygame so everyone could see the code running visually. The simulator uses all the concepts that we saw on the last posts of this series.

## The Game

Game of Life is a game without players. It represents the dynamic of interaction between a group of living beings at a bidimensional plane starting from an initial state and following 4 simple interaction rules.

This plane looks like a table divided into cells, and each cell can be in one of these two states, dead or alive. Besides that, the plane has an infinite dimension but, on our implementation, we going to have a limited space to simplify things.

The starting state we call seed and can you can define it manually or randomly.

The interaction cycle we call generation, and it is the result of the 4 interaction rules applied.

1. Every cell with less than two live neighbors dies by underpopulation;

    ![rule one](/img/exploring-pygame/rule-one.png)

2. Every live cell with two or three live neighbors survives to the next generation;

    ![rule two](/img/exploring-pygame/rule-two.png)

3. Every live cell with more than three neighbors dies of overpopulation;

    ![rule three](/img/exploring-pygame/rule-three.png)

4. Every dead cell with exactly three neighbors turns alive by reproduction.

    ![rule four](/img/exploring-pygame/rule-four.png)

There's no interaction between generations, so if a new cell is born when you go to compute the next cell, it won't count since it wasn't alive on this generation.

## Implementation

The simulator was made to test the game logic, and it was placed at the `simulator.py` file while the `game.py` file has the game of life logic.

The dojo challenge was the following, write a function `game_of_life` that receives a seed as a 50x50 matrix and return a matrix with the next generation computed:

{{< highlight python "linenos=table" >}}
# -*- coding: utf-8 -*-

def cell_check(section):
    '''
    Run the game of life rules at a 3x3 section to compute
    the middle cell
    '''
    # neighbor counter
    neighbors = 0
    # reference to the center of the section
    center = section[1][1]

    # sums all elements of the section
    for row in section:
        for cell in row:
            neighbors += cell

    # remove the center cell value to get only the
    # neighbor count
    neighbors -= center

    # applying the game of life rules
    # see that rule number two isn't effectively
    # applied since it doesn't change the state of the center cell
    if neighbors <= 1:
        # less than two neighbors, the cell dies of underpopulation
        center = 0
    elif neighbors == 3:
        # exactly three neighbors, the cell is born by reproduction
        center = 1
    elif neighbors >= 4:
        # more than three neighbors, the cell dies of overpopulation
        center = 0

    # returns the center cell value
    return center


def get_section(matrix, row, col):
    '''
    Extracts a 3x3 section from the plane given
    the center cell coordinate
    '''
    # builds an empty 3x3 section to make the plane section copy
    section = [[0 for _ in range(3)] for _ in range(3)]

    # runs through the plane section copying its values
    for sec_r, r in enumerate(range(row-1, row+2)):
        for sec_c, c in enumerate(range(col-1, col+2)):
            if r >= 0 and c >= 0 and r < 50 and c < 50:
                section[sec_r][sec_c] = matrix[r][c]

    # returns the copied 3x3 section
    return section


def game_of_life(seed):
    '''
    Receives the 50x50 plane seed, runs the game of life rules
    and returns the next generation
    '''
    # creates an empty plane to store the new generation
    # we don't mutate the seed to prevent side effects
    next_gen = [[0 for _ in range(50)] for _ in range(50)]

    # walks through the plane computing the next generation
    # of each cell
    for r, row in enumerate(seed):
        for c, col in enumerate(row):
            next_gen[r][c] = cell_check(get_section(seed, r, c))

    # returns the next generation
    return next_gen
{{< / highlight >}}

As you can see the `game.py` code computes only the next generation of the seed which is perfect to use at the `simulator.py` because we can compute the next generation to display on the screen and then feed the computed generation as a seed to the next one and so forth:

{{< highlight python "linenos=table" >}}
# -*- coding: utf-8 -*-

import random
import time

import pygame

from game import game_of_life

# empty plane
SEED = [[0 for _ in range(50)] for _ in range(50)]

# Glider
SEED[23][24] = 1
SEED[24][25] = 1
SEED[25][23] = SEED[25][24] = SEED[25][25] = 1

# Glider gun
# SEED[20][30] = 1
# SEED[21][28] = SEED[21][30] = 1
# SEED[22][18] = SEED[22][19] = SEED[22][26] = SEED[22][27] = SEED[22][40] = SEED[22][41] = 1
# SEED[23][17] = SEED[23][21] = SEED[23][26] = SEED[23][27] = SEED[23][40] = SEED[23][41] = 1
# SEED[24][6]  = SEED[24][7]  = SEED[24][16] = SEED[24][22] = SEED[24][26] = SEED[24][27] = 1
# SEED[25][6]  = SEED[25][7]  = SEED[25][16] = SEED[25][20] = SEED[25][22] = SEED[25][23] = SEED[25][28] = SEED[25][30] = 1
# SEED[26][16] = SEED[26][22] = SEED[26][30] = 1
# SEED[27][17] = SEED[27][21] = 1
# SEED[28][18] = SEED[28][19] = 1

# Rich's p16
# SEED[19][20] = SEED[19][21] = SEED[19][22] = SEED[19][26] = SEED[19][27] = SEED[19][28] = 1
# SEED[20][19] = SEED[20][23] = SEED[20][25] = SEED[20][29] = 1
# SEED[21][19] = SEED[21][23] = SEED[21][25] = SEED[21][29] = 1
# SEED[22][18] = SEED[22][20] = SEED[22][21] = SEED[22][22] = SEED[22][23] = SEED[22][25] = SEED[22][26] = SEED[22][27] = SEED[22][28] = SEED[22][30] = 1
# SEED[23][18] = SEED[23][19] = SEED[23][29] = SEED[23][30] = 1
# SEED[26][22] = SEED[26][23] = SEED[26][22] = SEED[26][25] = SEED[26][26] = 1
# SEED[27][21] = SEED[27][23] = SEED[27][25] = SEED[27][27] = 1
# SEED[28][22] = SEED[28][26] = 1

# Random
# SEED = [[random.choice([0, 1]) for _ in range(50)] for _ in range(50)]

pygame.init()

screen = pygame.display.set_mode((550, 550))


def draw_matrix(matrix):
    '''
    Function to draw the plane given the matrix
    '''

    # fills the screen with black
    screen.fill([0, 0, 0])

    # walks through the plane drawing its cells
    for r, row in enumerate(matrix):
        for c, cell in enumerate(row):
            if cell:
                # if the cell is alive draw it as a white square
                pygame.draw.rect(
                    screen, (255, 255, 255), (11*c, 11*r, 10, 10)
                )


# sets the seed as one of the preset values at the top
seed = SEED

# draws the initial state of the seed
draw_matrix(seed)

pygame.display.flip()

# waits one second so we can see the initial state before
# starting the interaction loop
time.sleep(1)

while True:
    # INPUT PROCESSING
    event = pygame.event.poll()

    if event.type == pygame.QUIT:
        # quits the program if clicked to close the window
        break
    elif (event.type == pygame.KEYDOWN and event.key == pygame.K_ESCAPE):
        # quits the program on pressing ESC
        break

    # GAME UPDATE

    # runs the game of life to get the next generation
    seed = game_of_life(seed)

    # DRAWING

    # draws the new generation at the screen
    draw_matrix(seed)

    pygame.display.flip()

    # waits a brief moment until going to the next generation
    time.sleep(0.05)
{{< / highlight >}}

The first drawing is separate from the game loop so the user can see the starting state before running the interactions on a faster pace. Entering inside the game loop we have the input processing step where quit conditions are checked so, moving to the game update step, the next generation is computed, and then, on the drawing step, the new generation is displayed at the screen.

## Examples

To demonstrate the simulator working, I choose the [glider](https://goo.gl/jzm5UZ) there is the most famous pattern inside the game of life. The hacker community absorbed it as their emblem, and it is the smaller cyclic pattern that moves on the plane:

{{< videogif "/img/exploring-pygame/glider.webm" >}}

{{% tip class="info" %}}
Note there are some commented patterns at the simulator code, you can uncomment them to see different patterns in action.
{{% /tip %}}

## Conclusion

The Game of Life is a great topic to learn programming concepts, and I recommend you to watch the [interview with its creator by the Numberphile](https://www.youtube.com/playlist?list=PLt5AfwLFPxWIL8XA1npoNAHseS-j1y-7V) youtube channel.
