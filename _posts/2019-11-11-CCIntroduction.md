---
layout: post
title: Introduction
tag: Chromecast game
---

In this tutorial you will learn how to add chromecast support to a browser game. The game used in this tutorial is the game that was created in my other tutorial [Multiplayer browser game for smartphones]().

The tutorial is divided into several sections:
1. Sender
2. Receiver
3. Limitations

### Setup

In this tutorial we are going to create a custom receiver app for the chromecast. The chromecast will load your app using a http request. During development the URL can use HTTP but when the app is published it has to use HTTPS. For development I recommend using [Heroku](https://www.heroku.com/).

### Next

[Implementing the chromecast API]()
