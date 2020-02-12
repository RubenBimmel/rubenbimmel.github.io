---
layout: post
title: Gamemanager
img: images/gamemanager.png
forward: Rooms
back: SocketIO
tag: Creating a multiplayer game like Jackbox
---

In this part we are going to add the game logic to the server by creating a game manager.

So far we added two modules to our Node server, Express and SocketIO. In this part of the tutorial we are going to write our own module.

Our gamemanager will keep a list of all active rooms. Every action a player does will be computed by the server. When the state of the game changes it will send the new state to all players in the room.

## Creating a gamemanager module

Create a new script inside your projects root folder and call it `gamemanager.js`. Inside this script we are going to write a simple game class. Add the following lines of code to `gamemanager.js`.

```js
class Game {
    constructor(id, host) {
        this.id = id;
        this.host = host;
        this.players = [];
    }
}
```

We can now easily create multiple games on the server. Let's add two functions to `gamemanager.js`. One to create a new room and one to join a room. For the room id we will generate a number between 0000 and 9999 and make sure it does not exist yet.

```js
var games = {};

function createRoom() {
    var id;
    do { 
        id = `0000${Math.floor(Math.random() * 10000)}`.slice(-4);
    } while (games[id] != undefined);
    
    games[id] = new Game(id);
    return games[id];
}
```

Let's add our module to the server. Add the following line to `index.js`.

```js
var gamemanager = require('./gamemanager.js');
```

We now have a gamemanager on the server. However we can not use it yet. We need to define what the gamemanager object is going to be. We can do this using the modules.exports function. Add the following lines of code to `gamemanager.js`.

```js
module.exports = {
    createRoom: createRoom
}
```

Now we can create a new room. Go to `index.js` and replace the host function with the following code:

```js
socket.on('host', function() {
    var game = gamemanager.createRoom(socket.id);
    socket.emit('room', game.id);
});
```

Now if we go to <a href="http://localhost:3000/game" target="_blank">http://localhost:3000/game</a> we should see a new room id appear every time we refresh the page.

So we are now able to create new games. Let's also create a function to remove a game. Add this function to `gamemanager.js`.

```js
function removeGame(id) {
    delete games[id];
}
```

We also need to add this function to the exports object.

```js
module.exports = {
    createRoom: createRoom,
    removeGame: removeGame
}
```

We want the game to stop when the connection with the host is lost. Add the following lines of code to the host event in `index.js`.

```js
socket.on('host', function() {
    var game = gamemanager.createRoom(socket.id);
    socket.emit('room', game.id);
    socket.on('disconnect', () => gamemanager.removeGame(game.id));
});
```

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/04-Gamemanager)

## Learn more about Node modules