---
layout : post
title : "Calculator Games"
date : 2018-5-27 20:42:00
categories : "Game Development"
---

About a month ago I needed to buy a calculator for a test I was going to take.
I bought the TI-84 Plus CE and while messing around with it, I discovered that
you could write programs for it. I made a couple of games that I presented for
my 11th grade end of the year project.

## TI-Pong
The first game I made was a clone of the 1972 Atari game, PONG. You can find a 
link the GitHub page [here](https://github.com/ReeCocho/TI-Pong) (Build
instructions are included in the repo.) I finished it in a few hours on the 
weekend. The game is controlled with the arrow keys, and you play against
an AI. The AI is really simple, it just moves to match the Y position of the 
ball. 

## TI-Space
The second game was a combination of Asteroids and Space Invaders. Again,
you can find the GitHub page [here](https://github.com/ReeCocho/TI-Space).
This one took me a lot longer to finish. It uses an infinite scrolling star
field without allocating any new memory, and basic collision detection 
between lasers, the player, and the asteroids. 

## Summary
Writing programs for such a limited platform was an exciting experience for me.
Since the platforms I usually make games for have an average of 8 GB of RAM 
and processor speeds of 4 GHz, I was forced to adapt to the limited environment.
I'd only ever read about running out of RAM and dealing with individual put_pixel
calls, but these things became common place when programming for the calculator.
I'd encourage anyone wishing to experience something new or flex their mental
muscles to try making a game for a platform like the calculator.