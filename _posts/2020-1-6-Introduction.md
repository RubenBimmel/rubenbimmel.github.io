---
layout: post
title: Introduction
img: images/introduction.png
forward: NodeJS
tag: Creating a multiplayer game like Jackbox
---

In this step-by-step tutorial we will create a multiplayer web game with a similar structure as Jackbox games.

Jackbox games are popular party games that run on a variety of platforms. Players can join a game using their smartphones. To do this you simply have to go to their website and enter the room name of the game you want to join. You can then use the browser screen on your phone as a controller. Unlike Jackbox games we won't develop this game for multiple platforms. Instead both the game and the controller will run inside a browser. 

There are more tutorials out there explaining how to create a multiplayer game for web and I will link some examples in this tutorial. This tutorial focusses on the networking for creating a game with separate controllers and a shared game screen. It is a basic setup that you can easily build upon to start developing your own party game.

## Who is this for

This tutorial is aimed at game developers that are new, or want to learn more, about developing multiplayer games in HTML and JavaScript. Getting started with developing multiplayer games for the web is not that hard but it requires some basic knowledge of a variety of tools and frameworks. In this tutorial I will explain the basics of NodeJS, Express and SocketIO and explain you how to use them to setup a simple server for a multiplayer game.

## Our goal

![End result]({{site.baseurl}}/images/preview.png)

This tutorial mainly focuses on the technical steps of setting up a multiplayer game like this. The result of this tutorial will be a simple rock paper scissors game. The game consists of three different webpages:
- the <b>homepage</b> - a screen where players can enter their name and join or host a room
- a <b>game</b> screen - a shared screen that hosts our game
- a <b>controller</b> - a controller screen that players get when they joined a game

## What you need to know
To be able to follow this tutorial you should have some basic knowledge on the fundamentals of web development. If you are new to the following programming languages I recommend reading these tutorials:

[>> HTML Basics - MDN web docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/HTML_basics)

[>> CSS Basics - MDN web docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/CSS_basics)

[>> Javasctipt Basics - MDN web docs](https://developer.mozilla.org/en-US/docs/Learn/Getting_started_with_the_web/JavaScript_basics)

Developing this game also requires a code editor that supports html and js. It does not matter which one you use. If you do not have any code editor yet I can highly recommend [Visual studio code](https://code.visualstudio.com/). It is a nice and lightweight code editor and is free to use.
