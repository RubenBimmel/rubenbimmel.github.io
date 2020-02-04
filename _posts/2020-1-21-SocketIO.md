---
layout: post
title: Socket.io
img: images/socketIO.png
forward: Gamemanager
back: Express
tag: Creating a multiplayer game like Jackbox
---

The next step in this tutorial is to provide communication between the server and the clients. To do this we are going to use a module called SocketIO. 

This is a very easy to use module that will allow us to use websockets. Websockets is a tool to communicate between a browser and a server. Sending messages is very fast wich makes it the perfect communication tool for creating multiplayer games in a browser. For now we will just do a basic implementation. This part will be similar to the [getting started](https://socket.io/get-started/chat/) tutorial from socketIO.

This tutorial continues with the code from the previous part. You can get the finished project from the last part [here](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/02-Express).

## Adding websocket communication
Just like any other module we first need to install it inside our project. Run the following command inside your project folder

```
c:\MultiplayerGame> npm install socket.io
```

Now we first need to setup socketIO on our server. Add the following line of code to your `index.js` file to implement SocketIO on the server.

```js
var io = require('socket.io').listen(server);
```

Now let us add some code to test if our server is working. The easiest way to do this is to log every time a user connects to the server. We can do this by listening to the io connection event. Add the following lines of code to `index.js`.

```js
io.on('connection', function(socket){
  console.log('a user connected');
});
```

Now it's time to add some JS to our html pages. Create a new folder inside the public folder and name it `js`. Create a new file inside that folder and name it `controller.js`. 

Add the following line to implement SocketIO on the client side.

```js
var socket = io();
```

We need to add this script to the client page. Add the following lines to `controller.html`.

```html
<script src="/socket.io/socket.io.js"></script>
<script src="js/controller.js"></script>
```

Now go to <a href="http://localhost:3000/controller" target="_blank">http://localhost:3000/controller</a>. If everything went right we should see no errors in the console. Inside your command line window you should see the log `a user connected` appear every time you load the page.

We can also log when a user disconnects from the server. To do this we need to listen to the disconnect event for each connection. We can start listening to these events by adding the following lines to `index.js`. After that both connection and disconnect events should be logged in the command line window.

```js
  socket.on('disconnect', function(){
    console.log('user disconnected');
  });
```

## Sending messages to the server

Now let's try sending messages from the client to the server. The controller page has an array of buttons that call a mark() function. Let us add this function to `controller.js` and log the results.

```js
function mark(position) {
  console.log(position);
}
```

To send this value to the server we can simply add the following line of code to the function.

```js
  socket.emit('mark', position);
```

Now we can also log this value on the server by listening to this event. Add the following lines of code to `index.js`.

```js
  socket.on('mark', function(position) {
    console.log(position);
  });
```

Now refresh the controller page and hit the buttons. If everything went right you can see the positions showing up inside your command line window.

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/03-SocketIO)

## Learn more about Socket.IO

We now have a websocket connection between the server and the client. In the upcomming parts we will create our game. We will use SocketIO a few more times during this project.

If you want to learn more about SocketIO on your own you can start by reading the docs here:

[SocketIO - docs](https://socket.io/docs/)

Also from here you can check out this great tutorial for a multiplayer game using SocketIO. It uses the same setup as we have done now. This tutorial explains how to use SocketIO to create a 2D game with player movement.

[Simple multiplayer game with SocketIO](https://medium.com/@projectyang/simple-multiplayer-game-with-socket-io-tutorial-part-one-setup-and-movement-ee202024f0ef)