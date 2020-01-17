---
layout: post
title: Socket.io
forward: Gamemanager
back: Express
tag: Creating a multiplayer game like Jackbox
---

This tutorial continues with the code from the previous part. You can get the finished project from the last part [here](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/02-Express).

The next step in this tutorial is to provide communication between the server and the clients. To do this we are going to use a module called SocketIO. This is a very easy to use module that will allow us to use websockets. Websockets is a tool to communicate between a browser and a server. Sending messages is very fast wich makes it the perfect communication tool for creating multiplayer games in a browser.

## Adding websocket communication
Just like any other module we first need to install it inside our project. Run the following command inside your project folder

```
c:\MultiplayerGame> npm install socket.io
```

Now we first need to setup socketIO on our server. Add the following lines of code to your `index.js` file.

```js
var io = require('socket.io')(http);

io.on('connection', function(socket){
  console.log('a user connected');
});
```

Now it's time to add some JS to our html pages. Create a new folder inside the public folder and name it `js`. Create a new file inside that folder and name it `controller.js`

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/03-SocketIO)

## Further readings