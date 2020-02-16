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

<span class="note">This tutorial continues with the code from the previous part. You can get the finished project from the last part [here](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/03-SocketIO).</note>

## Creating a gamemanager module

Create a new script inside your projects root folder and call it `gamemanager.js`. Inside this script we are going to write a simple game class. Add the following lines of code to `gamemanager.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="kd">class</span> <span class="nx">Game</span> <span class="p">{</span>
    <span class="kd">constructor</span><span class="p">(</span><span class="nx">id</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">id</span> <span class="o">=</span> <span class="nx">id</span><span class="p">;</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span> <span class="o">=</span> <span class="p">[];</span>
    <span class="p">}</span>
<span class="p">}</span></span>
</code></pre></div></div>

This class will be able to keep track of the game state on the server. Let's add two functions to this class that allows us to add a host and players to the game. We want the gamemanager to know what sockets are connected to a game so that later we can send messages to them from within the game class.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">class</span> <span class="nx">Game</span> <span class="p">{</span>
    <span class="kd">constructor</span><span class="p">(</span><span class="nx">id</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">id</span> <span class="o">=</span> <span class="nx">id</span><span class="p">;</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span> <span class="o">=</span> <span class="p">[];</span>
    <span class="p">}</span>

    <span class="new"><span class="nx">addHost</span><span class="p">(</span><span class="nx">socket</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">host</span> <span class="o">=</span> <span class="nx">socket</span><span class="p">.</span><span class="nx">id</span><span class="p">;</span>
        <span class="nx">socket</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">room</span><span class="dl">'</span><span class="p">,</span> <span class="k">this</span><span class="p">.</span><span class="nx">id</span><span class="p">);</span>
    <span class="p">}</span>

    <span class="nx">addPlayer</span><span class="p">(</span><span class="nx">socket</span><span class="p">,</span> <span class="nx">name</span><span class="p">)</span> <span class="p">{</span>
        <span class="kd">var</span> <span class="nx">player</span> <span class="o">=</span> <span class="p">{</span>
            <span class="na">id</span><span class="p">:</span> <span class="nx">socket</span><span class="p">.</span><span class="nx">id</span><span class="p">,</span>
            <span class="na">name</span><span class="p">:</span> <span class="nx">name</span>
        <span class="p">};</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">push</span> <span class="p">(</span><span class="nx">player</span><span class="p">);</span>
    <span class="p">}</span></span>
<span class="p">}</span>
</code></pre></div></div>

We can now easily create multiple games on the server. Let's add another function to `gamemanager.js` outside the game class to create a new room. For the room id we will generate a number between 0000 and 9999 and make sure it does not exist yet.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="kd">var</span> <span class="nx">games</span> <span class="o">=</span> <span class="p">{};</span>

<span class="kd">function</span> <span class="nx">createRoom</span><span class="p">()</span> <span class="p">{</span>
    <span class="kd">var</span> <span class="nx">id</span><span class="p">;</span>
    <span class="k">do</span> <span class="p">{</span> 
        <span class="nx">id</span> <span class="o">=</span> <span class="s2">`0000</span><span class="p">${</span><span class="nb">Math</span><span class="p">.</span><span class="nx">floor</span><span class="p">(</span><span class="nb">Math</span><span class="p">.</span><span class="nx">random</span><span class="p">()</span> <span class="o">*</span> <span class="mi">10000</span><span class="p">)}</span><span class="s2">`</span><span class="p">.</span><span class="nx">slice</span><span class="p">(</span><span class="o">-</span><span class="mi">4</span><span class="p">);</span>
    <span class="p">}</span> <span class="k">while</span> <span class="p">(</span><span class="nx">games</span><span class="p">[</span><span class="nx">id</span><span class="p">]</span> <span class="o">!=</span> <span class="kc">undefined</span><span class="p">);</span>
    
    <span class="nx">games</span><span class="p">[</span><span class="nx">id</span><span class="p">]</span> <span class="o">=</span> <span class="k">new</span> <span class="nx">Game</span><span class="p">(</span><span class="nx">id</span><span class="p">);</span>
    <span class="k">return</span> <span class="nx">games</span><span class="p">[</span><span class="nx">id</span><span class="p">];</span>
<span class="p">}</span></span>
</code></pre></div></div>

<div class="fold-out" onclick="this.classList.toggle('open')">
<div class="fold-out-title">Why are we using var games = {} ?</div>
<div class="fold-out-content">We create a games object to store all of our active games. By storing them in an object instead of an array we can add them using the id string as key. We could loop over these games by using Objec.values(games).</div>
</div>

<div class="fold-out" onclick="this.classList.toggle('open')">
<div class="fold-out-title">How is this room id generated?</div>
<div class="fold-out-content">To generate the room id we use the Math.random() function. It generates a number between 0 and 1. By multiplying it by 10000 and using the Math.floor() function we generate a whole number between 0 and 9999. <br/><br/>We want to add 0 to the left side of the number so that there are always four digits. To do this we can add four 0s before the number and take the last 4 numbers of the result. For example 42 becomes 000042 and then the last four digits are 0042.</div>
</div>

Let's add our module to the server. Add the following line to `index.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="kd">var</span> <span class="nx">io</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="dl">'</span><span class="s1">socket.io</span><span class="dl">'</span><span class="p">).</span><span class="nx">listen</span><span class="p">(</span><span class="nx">server</span><span class="p">);</span>
<span class="new"><span class="kd">var</span> <span class="nx">gamemanager</span> <span class="o">=</span> <span class="nx">require</span><span class="p">(</span><span class="dl">'</span><span class="s1">./gamemanager.js</span><span class="dl">'</span><span class="p">);</span></span>

<span class="kd">const</span> <span class="nx">port</span> <span class="o">=</span> <span class="mi">3000</span><span class="p">;</span>
</code></pre></div></div>

We now have a gamemanager on the server. However we can not use it yet. We need to define what the gamemanager object is going to be. We can do this using the `modules.exports` function. Add the following lines of code to `gamemanager.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">createRoom</span><span class="p">:</span> <span class="nx">createRoom</span>
<span class="p">}</span></span>
</code></pre></div></div>

Now we can use our gamemanager to create a new room. Go to `index.js` and replace the host function with the following code:

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">io</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">connection</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">socket</span><span class="p">){</span>
  <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">a user connected</span><span class="dl">'</span><span class="p">);</span>

  <span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">disconnect</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(){</span>
    <span class="nx">console</span><span class="p">.</span><span class="nx">log</span><span class="p">(</span><span class="dl">'</span><span class="s1">user disconnected</span><span class="dl">'</span><span class="p">);</span>
  <span class="p">});</span>

  <span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">host</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
    <span class="new"><span class="kd">var</span> <span class="nx">game</span> <span class="o">=</span> <span class="nx">gamemanager</span><span class="p">.</span><span class="nx">createRoom</span><span class="p">(</span><span class="nx">socket</span><span class="p">.</span><span class="nx">id</span><span class="p">);</span>
    <span class="nx">game</span><span class="p">.</span><span class="nx">addHost</span><span class="p">(</span><span class="nx">socket</span><span class="p">);</span></span>
  <span class="p">});</span>
<span class="p">});</span>
</code></pre></div></div>

Now if we go to <a href="http://localhost:3000/game" target="_blank">http://localhost:3000/game</a> we should see a new room id appear every time we refresh the page.

![Room number]({{site.baseurl}}/images/room9548.png)

## Removing rooms

So we are now able to create new games. Let's also create a function to remove a game. Add this function to `gamemanager.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="kd">function</span> <span class="nx">removeRoom</span><span class="p">(</span><span class="nx">id</span><span class="p">)</span> <span class="p">{</span>
    <span class="k">delete</span> <span class="nx">games</span><span class="p">[</span><span class="nx">id</span><span class="p">];</span>
<span class="p">}</span></span>
</code></pre></div></div>

We also need to add this function to the exports object.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">module</span><span class="p">.</span><span class="nx">exports</span> <span class="o">=</span> <span class="p">{</span>
    <span class="na">createRoom</span><span class="p">:</span> <span class="nx">createRoom</span><span class="new"><span class="p">,</span>
    <span class="na">removeRoom</span><span class="p">:</span> <span class="nx">removeRoom</span></span>
<span class="p">}</span>
</code></pre></div></div>

We want the game to stop when the host loses connection. Add the following lines of code to the host event in `index.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code>  <span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">host</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
    <span class="kd">var</span> <span class="nx">game</span> <span class="o">=</span> <span class="nx">gamemanager</span><span class="p">.</span><span class="nx">createRoom</span><span class="p">(</span><span class="nx">socket</span><span class="p">.</span><span class="nx">id</span><span class="p">);</span>
    <span class="nx">game</span><span class="p">.</span><span class="nx">addHost</span><span class="p">(</span><span class="nx">socket</span><span class="p">);</span>
    <span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">disconnect</span><span class="dl">'</span><span class="p">,</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="nx">gamemanager</span><span class="p">.</span><span class="nx">removeRoom</span><span class="p">(</span><span class="nx">game</span><span class="p">.</span><span class="nx">id</span><span class="p">));</span></span>
  <span class="p">});</span>
<span class="p">});</span>
</code></pre></div></div>

<div class="fold-out" onclick="this.classList.toggle('open')">
<div class="fold-out-title">Why are we adding this listener inside the host listener?</div>
<div class="fold-out-content">By adding this listener inside the host listener this function will only listen to disconnection events on hosts. This reduces the amount of listeners added to sockets. Adding to many listeners can result in performance issues.</div>
</div>

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/04-Gamemanager)

## Learn more about Node modules

By using the modules.exports object we can easily create our own modules and add them to our server. If you would like to see more example of how to use modules I recommend reading this great and very complete blogpost:

[>> stackabuse - how to use module.exports in nodeJS](https://stackabuse.com/how-to-use-module-exports-in-node-js/)

In the next part of this tutorial we will continue working on this module and improve our game class so that players can actually join our game.