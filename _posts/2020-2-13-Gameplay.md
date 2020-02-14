---
layout: post
title: Gameplay
img: images/placeholder2.png
back: Lobby
tag: Creating a multiplayer game like Jackbox
---

Now we are ready to add some gameplay to our game. We will continue playing rounds of rock paper and scissors untill one player remains.

First let's define the rules for playing rocks papers and scissors with more than two players. At the end of each round we will calculate the sum for each option. The option with the heighest sum wins the game. On top of that Rock beats an equal amount of scissors, scissors beats an equal amount of paper and paper beats an equal amount of rocks.

Now let's program the different states of our game and finally make it playable!

This tutorial continues with the code from the previous part. You can get the finished project from the last part [here](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/06-Lobby).

## betting on a hand

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/07-Gameplay)