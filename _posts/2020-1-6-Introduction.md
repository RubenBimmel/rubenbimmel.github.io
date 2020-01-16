---
layout: post
title: Introduction
forward: NodeJS
tag: Creating a multiplayer game like Jackbox
---

In this step-by-step tutorial we will create a multiplayer web game with a similar structure as Jackbox games.

Jackbox games are popular party games that run on a variety of platforms. Players can join a game using their smartphones. To do this you simply have to go to their website and enter the room name of the game you want to join. You can then use the browser screen on your phone as a controller. 

Unlike Jackbox games we won't develop this game for multiple platforms. Instead both the game and the controller will run inside a browser screen.

## Our goal

![End result]({{site.baseurl}}/images/placeholder.jpg)

This tutorial mainly focuses on the technical steps of setting up a multiplayer game like this. The end result of this tutorial will be a simple tic tac toe game. The game consists of three different webpages:
- the lobby
- a shared screen
- a controller

## Prerequities
This tutorial is aimed at game developers that are new, or want to learn more, about developing games in HTML and JavaScript. To be able to follow this tutorial you should have some basic knowledge on the fundamentals of web development. If you are new to the following programming languages I recommend reading these tutorials:

[HTML Basics - MDN web docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics)

[CSS Basics - MDN web docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)

[Javasctipt Basics - MDN web docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/JavaScript_basics)

Developing this game also requires a code editor that supports html and js. It does not matter which one you use. If you do not have any code editor yet I can highly recommend [Visual studio code](https://code.visualstudio.com/). It is a nice and lightweight code editor and is free to use.
