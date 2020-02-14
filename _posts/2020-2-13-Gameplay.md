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

Now that the betting state has started we can see the three buttons for rock, paper and scissors. Let's add a local state for these buttons so that players can pick one. We also emit the chosen hand to the server. Add the following lines of code to `controller.js`.

```js
var hand = document.getElementById("hand");

document.getElementById("rock").onclick = () => choose("rock");
document.getElementById("paper").onclick = () => choose("paper");
document.getElementById("scissors").onclick = () => choose("scissors");

function choose(option) {
    hand.classList = option;
    socket.emit('hand', option);
}
```

Now we can also store this value in our `gamemanager.js`.

```js
socket.on('hand', (hand) => {
    player.hand = hand;
});
```

Let's also visualise this in our game screen. We'll reuse the same `active` class as we used in our lobby. First we remove the active class as soon as the round starts.

```js
setState(state) {
    this.state = state;
    this.room.emit('state', this.state);

    switch(state) {
        case "betting":
            this.room.emit('reset', this.players);
            break;
    }
}
```

In `game.js` we can listen to this event and remove the ready class from all players.

```js
socket.on('reset', function(playerList) {
    playerList.forEach(p => {
        players[p.id].classList.remove('active');
    });
})
```

Then we notify the host that a player has chosen a hand. It does not have to know what it is as that could spoil it for any other players. Let's just reuse the ready event.

```js
socket.on('hand', (hand) => {
    player.hand = hand;
    this.room.to(this.host).emit('ready', player);
});
```

Now our game screen also reacts when a player has chosen a hand.

## Showing results

Once every player has choosen a hand we can resolve the game. Let's start by calculating the sum of all hands. Add the following lines of code to `gamemanager.js`. First we update the game state once every player has chosen a hand.

```js
socket.on('hand', (hand) => {
    player.hand = hand;
    this.room.to(this.host).emit('ready', player);

    var unready = this.players.find(p => !p.hand);
    if (!unready) this.setState("resolving");
});
```

Then we calculate the sums and send the results to the host.

```js
switch(state) {
    case "betting":
        this.room.emit('reset', this.players);
        break;
    case "resolving":
        var result = {rock: 0, paper: 0, scissors: 0};
        this.players.forEach(p => {
            if (p.hand == "rock") result.rock ++;
            else if (p.hand == "paper") result.paper ++;
            else if (p.hand == "scissors") result.scissors ++;
        })
        this.room.to(this.host).emit('result', result);
        break;
}
```

Now we can show the results on the game screen. Add the following lines of code to `game.js`.

```js
socket.on('result', function(result) {
    rockValue.innerText = result.rock;
    paperValue.innerText = result.paper;
    scissorsValue.innerText = result.scissors;
});
```

Now we can see the results of the round appear in the left panel. Let's also show the correct hand for each player. Change the object so that it also passes the list of players.

```js
case "resolving":
    var result = {rock: 0, paper: 0, scissors: 0, players: this.players};
    this.players.forEach(p => {
        if (p.hand == "rock") result.rock ++;
        else if (p.hand == "paper") result.paper ++;
        else if (p.hand == "scissors") result.scissors ++;
    })
    this.room.to(this.host).emit('result', result);
    break;
```

Now we can loop over all the players inside `game.js` and apply the right hand to all the players.

```js
socket.on('result', function(result) {
    rockValue.innerText = result.rock;
    paperValue.innerText = result.paper;
    scissorsValue.innerText = result.scissors;

    result.players.forEach(p => {
        players[p.id].classList.add(p.hand);
    });
})
```

## Starting a new round

Now that the round is finished we can calculate our winners. We will add a lost property to all players that did not have a winning hand. Add the following lines of code to `gamemanager.js`.

```js
addPlayer(socket, name) {
    var player = {
        id: socket.id,
        name: name,
        lost: false
    };
```

```js
case "resolving":
    var result = {rock: 0, paper: 0, scissors: 0, players: this.players};
    this.players.forEach(p => {
        if (p.hand == "rock") result.rock ++;
        else if (p.hand == "paper") result.paper ++;
        else if (p.hand == "scissors") result.scissors ++;
    });
    this.room.to(this.host).emit('result', result);

    var winner;
    if (result.rock > result.paper && result.rock >= result.scissors) winner = "rock";
    if (result.paper > result.scissors && result.paper >= result.rock) winner = "paper";
    if (result.scissors > result.rock && result.scissors >= result.paper) winner = "scissors";

    if (winner) {
        this.players.forEach(p => {
            if (p.hand != winner) p.lost = true;
        })
    }

    setTimeout(() => this.reset(), 3000);
    break;
```

The last line of that code above will automaticly call a restart function after 3 seconds to start the next round. Let's add the reset function to our `gamemanager.js`.

```js
reset() {
    this.players.forEach(p => p.hand = null);
    this.setState("betting");
}
```

We need to properly reset our game and controller screens as well. Update the reset event listener in `game.js` like this.

```js
socket.on('reset', function(playerList) {
    playerList.forEach(p => {
        players[p.id].classList.remove('active', 'rock', 'paper', 'scissors');
    });

    rockValue.innerText = paperValue.innerText = scissorsValue.innerText = "";
})
```

And also add a listener to the reset event in our `controller.js`.

```js
socket.on('reset', function() {
    hand.classList.remove('active', 'rock', 'paper', 'scissors');
})
```

We are now starting a new round after all results have been calculated. However player that have lost are still able to participate. Update the hand event listener inside our `gamemanager.js` so that it ignores these players.

```js
socket.on('hand', (hand) => {
    if (player.lost) return;

    player.hand = hand;
    this.room.to(this.host).emit('ready', player);

    var unready = this.players.find(p => !p.hand && !p.lost);
    if (!unready) this.setState("resolving");
});
```

Now players that have lost are excluded in the next rounds. Let's also visualise this on the game screen. Let's pass the list of players in the reset function so that we can check reset it to a lost icon when the player can't participate. Add the following line of code to the reset function in `game.js`.

```js
socket.on('reset', function(playerList) {
    playerList.forEach(p => {
        players[p.id].classList.remove('active', 'rock', 'paper', 'scissors');
        players[p.id].classList.toggle('lost', p.lost);
    });

    rockValue.innerText = paperValue.innerText = scissorsValue.innerText = "";
})
```

## The final winner

Once there is only one player left we can declare our final winner. To do this let's check the player count in the reset function. If there is only player left that has not lost we can do a big reset where we set everyones lost state to false again. Let's also emit an event to the host so we can display the winner in our game screen.

```js
reset() {
    this.players.forEach(p => p.hand = null);
    if (this.players.filter(p => !p.lost).length == 1) {
        this.room.to(this.host).emit('winner', this.players);
        this.players.forEach(p => p.lost = false);
    }
    this.setState("betting");
}
```

Finally add the last bit of code to `game.js`.

```js
socket.on('winner', function(results) {
    results.forEach(p => players[p.id].classList.toggle('winner', !p.lost));
});
```

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/07-Gameplay)