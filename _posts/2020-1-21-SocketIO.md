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

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">const</span> <span class="nx">app</span> <span class="o">=</span> <span class="nx">express</span><span class="p">();</span>
<span class="kd">const</span> <span class="nx">server</span> <span class="o">=</span> <span class="nx">http</span><span class="p">.</span><span class="nx">createServer</span><span class="p">(</span><span class="nx">app</span><span class="p">);</span>

<span class="new"><span class="kd">var</span> <span class="nx">io</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="dl">'</span><span class="s1">socket.io</span><span class="dl">'</span><span class="p">).</span><span class="nx">listen</span><span class="p">(</span><span class="nx">server</span><span class="p">);</span></span>

<span class="kd">const</span> <span class="nx">port</span> <span class="o">=</span> <span class="mi">3000</span><span class="p">;</span>
</code></pre></div></div>

Now let us add some code to test if our server is working. The easiest way to do this is to log every time a user connects to the server. We can do this by listening to the io connection event. Add the following lines of code to `index.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="nx">io</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">connection</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">socket</span><span class="p">){</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">a user connected</span><span class="dl">'</span><span class="p">);</span>
<span class="p">});</span></span>
</code></pre></div></div>

Now it's time to add some JS to our html pages. Create a new folder inside the public folder and name it `js`. Create a new file inside that folder and name it `game.js`. this will be the script that runs in `game.html`, the shared screen of our game.

Add the following line to implement SocketIO on the client side.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="kd">var</span> <span class="nx">socket</span> <span class="o">=</span> <span class="nx">io</span><span class="p">();</span></span>
</code></pre></div></div>

We need to add this script to the client page. Add the following lines to `game.html`. It is important that the socketIO script is added in the header.

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code>    <span class="nt">&lt;title&gt;</span>Rock Paper Scissors<span class="nt">&lt;/title&gt;</span>
    <span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"stylesheet"</span> <span class="na">href=</span><span class="s">"css/stylesheet.css"</span><span class="nt">&gt;</span>
    <span class="new"><span class="nt">&lt;script </span><span class="na">src=</span><span class="s">"/socket.io/socket.io.js"</span><span class="nt">&gt;&lt;/script&gt;</span></span>
<span class="nt">&lt;/head&gt;</span>
</code></pre></div></div>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code>      <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;/div&gt;</span>

  <span class="new"><span class="nt">&lt;script </span><span class="na">src=</span><span class="s">"js/game.js"</span><span class="nt">&gt;&lt;/script&gt;</span></span>
<span class="nt">&lt;/body&gt;</span>
</code></pre></div></div>

Now go to <a href="http://localhost:3000/game" target="_blank">http://localhost:3000/game</a>. If everything went right we should see no errors in the console. Inside your command line window you should see the log `a user connected` appear every time you load the page.

We can also log when a user disconnects from the server. To do this we need to listen to the disconnect event for each connection. We can start listening to these events by adding the following lines to `index.js`. After that both connection and disconnect events should be logged in the command line window.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">io</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">connection</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">socket</span><span class="p">){</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">a user connected</span><span class="dl">'</span><span class="p">);</span>

  <span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">disconnect</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(){</span>
    <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">user disconnected</span><span class="dl">'</span><span class="p">);</span>
  <span class="p">});</span></span>
<span class="p">});</span>
</code></pre></div></div>

Let's also add socketIO to `controller.html`. Inside the `js` folder create a new file named `controller.js` and add the following lines of code to `controller.html`.

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code>  <span class="nt">&lt;title&gt;</span>Rock Paper Scissors - Controller<span class="nt">&lt;/title&gt;</span>
  <span class="nt">&lt;link</span> <span class="na">rel=</span><span class="s">"stylesheet"</span> <span class="na">href=</span><span class="s">"css/stylesheet.css"</span><span class="nt">&gt;</span>
  <span class="new"><span class="nt">&lt;script </span><span class="na">src=</span><span class="s">"/socket.io/socket.io.js"</span><span class="nt">&gt;&lt;/script&gt;</span></span>
<span class="nt">&lt;/head&gt;</span>
</code></pre></div></div>

<div class="language-html highlighter-rouge"><div class="highlight"><pre class="highlight"><code>      <span class="nt">&lt;/div&gt;</span>
  <span class="nt">&lt;/div&gt;</span>

  <span class="new"><span class="nt">&lt;script </span><span class="na">src=</span><span class="s">"js/controller.js"</span><span class="nt">&gt;&lt;/script&gt;</span></span>
<span class="nt">&lt;/body&gt;</span>
</code></pre></div></div>

Let's also start a socket connection inside `controller.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="kd">var</span> <span class="nx">socket</span> <span class="o">=</span> <span class="nx">io</span><span class="p">();</span></span>
</code></pre></div></div>

## Sending messages to the server

We now have communication on both the controller and the shared screen of our game. The shared screen is going to host new games. The controller is going to join a game as a player. We want the server to know wheter a connected user is a host or a controller.

Let's start by sending a message from the game screen to the server. Add the following code to `game.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">var</span> <span class="nx">socket</span> <span class="o">=</span> <span class="nx">io</span><span class="p">();</span>

<span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">host</span><span class="dl">'</span><span class="p">);</span></span>
</code></pre></div></div>

Using the emit function we can send an event to the server. We can than respond on the server by adding this code to `index.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">io</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">connection</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">socket</span><span class="p">){</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">a user connected</span><span class="dl">'</span><span class="p">);</span>

  <span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">disconnect</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(){</span>
    <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">user disconnected</span><span class="dl">'</span><span class="p">);</span>
  <span class="p">});</span>

  <span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">host</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
    <span class="nx">socket</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">room</span><span class="dl">'</span><span class="p">,</span> <span class="dl">"</span><span class="s2">1234</span><span class="dl">"</span><span class="p">);</span>
  <span class="p">});</span></span>
<span class="p">});</span>
</code></pre></div></div>

We are responding to the client with a new event. We can read the data from this event in the client. Let's add some code to `game.js` so that we can display the room number.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">host</span><span class="dl">'</span><span class="p">);</span>

<span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">room</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">id</span><span class="p">)</span> <span class="p">{</span>
    <span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="dl">"</span><span class="s2">room</span><span class="dl">"</span><span class="p">).</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">id</span><span class="p">;</span>
<span class="p">});</span></span>
</code></pre></div></div>

Now if we go to <a href="http://localhost:3000/game" target="_blank">http://localhost:3000/game</a> we should see our new room number appear in the top left corner.

![Room number]({{site.baseurl}}/images/room1234.png)

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/03-SocketIO)

## Learn more about Socket.IO

We now have a websocket connection between the server and the clients. In the upcomming parts we will create our game and use more complex functions of socketIO.

If you want to learn more about SocketIO on your own you can start by reading the docs here:

[>> SocketIO - docs](https://socket.io/docs/)

Also from here you can check out this great tutorial for a multiplayer game using SocketIO. It uses a similar setup as we have done now. This tutorial explains how to use SocketIO to create a 2D game with player movement.

[>> Simple multiplayer game with SocketIO](https://medium.com/@projectyang/simple-multiplayer-game-with-socket-io-tutorial-part-one-setup-and-movement-ee202024f0ef)