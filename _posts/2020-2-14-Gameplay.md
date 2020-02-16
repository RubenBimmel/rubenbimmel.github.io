---
layout: post
title: Gameplay
img: images/gameplay.png
back: Lobby
tag: Creating a multiplayer game like Jackbox
---

Now we are ready to add some gameplay to our game. We will continue playing rounds of rock paper and scissors untill one player remains.

First let's define the rules for playing rocks papers and scissors with more than two players. At the end of each round we will calculate the sum for each option. The option that has the highest sum wins the game. On top of that Rock beats an equal amount of scissors, scissors beats an equal amount of paper and paper beats an equal amount of rocks. Now let's program the different states of our game and finally make it playable!

<span class="note">This tutorial continues with the code from the previous part. You can get the finished project from the last part [here](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/06-Lobby).</span>

## betting on a hand

Now that the betting state has started we can see the three buttons for rock, paper and scissors. Let's add a local state for these buttons so that players can pick one. We also emit the chosen hand to the server. Add the following lines of code to `controller.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="kd">var</span> <span class="nx">hand</span> <span class="o">=</span> <span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="dl">"</span><span class="s2">hand</span><span class="dl">"</span><span class="p">);</span>

<span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="dl">"</span><span class="s2">rock</span><span class="dl">"</span><span class="p">).</span><span class="nx">onclick</span> <span class="o">=</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="nx">choose</span><span class="p">(</span><span class="dl">"</span><span class="s2">rock</span><span class="dl">"</span><span class="p">);</span>
<span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="dl">"</span><span class="s2">paper</span><span class="dl">"</span><span class="p">).</span><span class="nx">onclick</span> <span class="o">=</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="nx">choose</span><span class="p">(</span><span class="dl">"</span><span class="s2">paper</span><span class="dl">"</span><span class="p">);</span>
<span class="nb">document</span><span class="p">.</span><span class="nx">getElementById</span><span class="p">(</span><span class="dl">"</span><span class="s2">scissors</span><span class="dl">"</span><span class="p">).</span><span class="nx">onclick</span> <span class="o">=</span> <span class="p">()</span> <span class="o">=&gt;</span> <span class="nx">choose</span><span class="p">(</span><span class="dl">"</span><span class="s2">scissors</span><span class="dl">"</span><span class="p">);</span>

<span class="kd">function</span> <span class="nx">choose</span><span class="p">(</span><span class="nx">option</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">hand</span><span class="p">.</span><span class="nx">classList</span> <span class="o">=</span> <span class="nx">option</span><span class="p">;</span>
    <span class="nx">socket</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">hand</span><span class="dl">'</span><span class="p">,</span> <span class="nx">option</span><span class="p">);</span>
<span class="p">}</span></span>
</code></pre></div></div>

Now we can select a hand by pressing one of the buttons. By updating the class of the hand object we make sure that the right button is highlighted. Now let's go back to our server. Let's store the chosen hand in the player object.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code>    <span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">ready</span><span class="dl">'</span><span class="p">,</span> <span class="p">(</span><span class="nx">ready</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nx">player</span><span class="p">.</span><span class="nx">ready</span> <span class="o">=</span> <span class="nx">ready</span><span class="p">;</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">ready</span><span class="dl">'</span><span class="p">,</span> <span class="nx">player</span><span class="p">);</span>

        <span class="k">if</span> <span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">length</span> <span class="o">&gt;=</span> <span class="mi">2</span><span class="p">)</span> <span class="p">{</span>
            <span class="kd">var</span> <span class="nx">unready</span> <span class="o">=</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">ready</span><span class="p">);</span>
            <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="nx">unready</span><span class="p">)</span> <span class="k">this</span><span class="p">.</span><span class="nx">setState</span><span class="p">(</span><span class="dl">"</span><span class="s2">betting</span><span class="dl">"</span><span class="p">);</span>
        <span class="p">}</span>
    <span class="p">});</span>

    <span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">hand</span><span class="dl">'</span><span class="p">,</span> <span class="p">(</span><span class="nx">hand</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nx">player</span><span class="p">.</span><span class="nx">hand</span> <span class="o">=</span> <span class="nx">hand</span><span class="p">;</span>
    <span class="p">});</span></span>
<span class="p">}</span>
</code></pre></div></div>

Let's also send an event to our game screen. This way we can see which players have already made a decision. We'll reuse the same `active` class as we used in our lobby. First we remove the active class as soon as the round starts. To do this we first remove the `active` class from all players when the round starts. Add the following lines of code to `gamemanager.js` to emit a reset event.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">setState</span><span class="p">(</span><span class="nx">state</span><span class="p">)</span> <span class="p">{</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">state</span> <span class="o">=</span> <span class="nx">state</span><span class="p">;</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">state</span><span class="dl">'</span><span class="p">,</span> <span class="k">this</span><span class="p">.</span><span class="nx">state</span><span class="p">);</span>

    <span class="new"><span class="k">switch</span><span class="p">(</span><span class="nx">state</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">case</span> <span class="dl">"</span><span class="s2">betting</span><span class="dl">"</span><span class="p">:</span>
            <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">reset</span><span class="dl">'</span><span class="p">,</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">);</span>
            <span class="k">break</span><span class="p">;</span>
    <span class="p">}</span></span>
<span class="p">}</span>
</code></pre></div></div>

In `game.js` we can listen to this event and remove the ready class from all players.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">reset</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">playerList</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">playerList</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nx">players</span><span class="p">[</span><span class="nx">p</span><span class="p">.</span><span class="nx">id</span><span class="p">].</span><span class="nx">classList</span><span class="p">.</span><span class="nx">remove</span><span class="p">(</span><span class="dl">'</span><span class="s1">active</span><span class="dl">'</span><span class="p">);</span>
    <span class="p">});</span>
<span class="p">})</span></span>
</code></pre></div></div>

Then we send an event to the host to let it know that a player has chosen a hand. It does not have to know what it is as that could spoil it for any other players. Let's just reuse the ready event. Add the following lines of code to `gamemanager.js`

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">hand</span><span class="dl">'</span><span class="p">,</span> <span class="p">(</span><span class="nx">hand</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="nx">player</span><span class="p">.</span><span class="nx">hand</span> <span class="o">=</span> <span class="nx">hand</span><span class="p">;</span>
    <span class="new"><span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">ready</span><span class="dl">'</span><span class="p">,</span> <span class="nx">player</span><span class="p">);</span></span>
<span class="p">});</span>
</code></pre></div></div>

Now our game screen also reacts when a player has chosen a hand.

## Showing results

Once every player has chosen a hand we can resolve the game. First we update the game state once every player has chosen a hand. Add the following lines of code to `gamemanager.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">hand</span><span class="dl">'</span><span class="p">,</span> <span class="p">(</span><span class="nx">hand</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="nx">player</span><span class="p">.</span><span class="nx">hand</span> <span class="o">=</span> <span class="nx">hand</span><span class="p">;</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">ready</span><span class="dl">'</span><span class="p">,</span> <span class="nx">player</span><span class="p">);</span>

    <span class="new"><span class="kd">var</span> <span class="nx">unready</span> <span class="o">=</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span><span class="p">);</span>
    <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="nx">unready</span><span class="p">)</span> <span class="k">this</span><span class="p">.</span><span class="nx">setState</span><span class="p">(</span><span class="dl">"</span><span class="s2">resolving</span><span class="dl">"</span><span class="p">);</span></span>
<span class="p">});</span>
</code></pre></div></div>

Then we calculate the sums and send the results to the host.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">switch</span><span class="p">(</span><span class="nx">state</span><span class="p">)</span> <span class="p">{</span>
    <span class="k">case</span> <span class="dl">"</span><span class="s2">betting</span><span class="dl">"</span><span class="p">:</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">reset</span><span class="dl">'</span><span class="p">,</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">);</span>
        <span class="k">break</span><span class="p">;</span>
    <span class="new"><span class="k">case</span> <span class="dl">"</span><span class="s2">resolving</span><span class="dl">"</span><span class="p">:</span>
        <span class="kd">var</span> <span class="nx">result</span> <span class="o">=</span> <span class="p">{</span><span class="na">rock</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">paper</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">scissors</span><span class="p">:</span> <span class="mi">0</span><span class="p">};</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
            <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">rock</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span> <span class="o">++</span><span class="p">;</span>
            <span class="k">else</span> <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">paper</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span> <span class="o">++</span><span class="p">;</span>
            <span class="k">else</span> <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">scissors</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span> <span class="o">++</span><span class="p">;</span>
        <span class="p">})</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">result</span><span class="dl">'</span><span class="p">,</span> <span class="nx">result</span><span class="p">);</span>
        <span class="k">break</span><span class="p">;</span></span>
<span class="p">}</span>
</code></pre></div></div>

Now we can show the results on the game screen. Add the following lines of code to `game.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">result</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">result</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">rockValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span><span class="p">;</span>
    <span class="nx">paperValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span><span class="p">;</span>
    <span class="nx">scissorsValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span><span class="p">;</span>
<span class="p">});</span></span>
</code></pre></div></div>

Now we can see the results of the round appear in the left panel. 

![Round results]({{site.baseurl}}/images/results.png)

Let's also show the correct hand for each player. Change the result object so that it contains the information of the players.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">case</span> <span class="dl">"</span><span class="s2">resolving</span><span class="dl">"</span><span class="p">:</span>
    <span class="new"><span class="kd">var</span> <span class="nx">result</span> <span class="o">=</span> <span class="p">{</span><span class="na">rock</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">paper</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">scissors</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">players</span><span class="p">:</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">};</span></span>
    <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">rock</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span> <span class="o">++</span><span class="p">;</span>
        <span class="k">else</span> <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">paper</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span> <span class="o">++</span><span class="p">;</span>
        <span class="k">else</span> <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">scissors</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span> <span class="o">++</span><span class="p">;</span>
    <span class="p">})</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">result</span><span class="dl">'</span><span class="p">,</span> <span class="nx">result</span><span class="p">);</span>
    <span class="k">break</span><span class="p">;</span>
</code></pre></div></div>

Now we can loop over all the players inside `game.js` and apply the right hand to all the players.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">result</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">result</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">rockValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span><span class="p">;</span>
    <span class="nx">paperValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span><span class="p">;</span>
    <span class="nx">scissorsValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span><span class="p">;</span>

    <span class="new"><span class="nx">result</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nx">players</span><span class="p">[</span><span class="nx">p</span><span class="p">.</span><span class="nx">id</span><span class="p">].</span><span class="nx">classList</span><span class="p">.</span><span class="nx">add</span><span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span><span class="p">);</span>
    <span class="p">});</span></span>
<span class="p">})</span>
</code></pre></div></div>

Now if we finish a round of our game we can see the images of the players change to their chosen hand.

![Round results]({{site.baseurl}}/images/results2.png)

## Starting a new round

Now it's time to calculate our winners. We will add a lost property to all players that did not have a winning hand. Then we can restart our game for the next round. Add the following lines of code to `gamemanager.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">addPlayer</span><span class="p">(</span><span class="nx">socket</span><span class="p">,</span> <span class="nx">name</span><span class="p">)</span> <span class="p">{</span>
    <span class="kd">var</span> <span class="nx">player</span> <span class="o">=</span> <span class="p">{</span>
        <span class="na">id</span><span class="p">:</span> <span class="nx">socket</span><span class="p">.</span><span class="nx">id</span><span class="p">,</span>
        <span class="na">name</span><span class="p">:</span> <span class="nx">name</span><span class="p">,</span>
        <span class="new"><span class="na">lost</span><span class="p">:</span> <span class="kc">false</span></span>
    <span class="p">};</span>
</code></pre></div></div>

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="k">case</span> <span class="dl">"</span><span class="s2">resolving</span><span class="dl">"</span><span class="p">:</span>
    <span class="kd">var</span> <span class="nx">result</span> <span class="o">=</span> <span class="p">{</span><span class="na">rock</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">paper</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">scissors</span><span class="p">:</span> <span class="mi">0</span><span class="p">,</span> <span class="na">players</span><span class="p">:</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">};</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">rock</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span> <span class="o">++</span><span class="p">;</span>
        <span class="k">else</span> <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">paper</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span> <span class="o">++</span><span class="p">;</span>
        <span class="k">else</span> <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">==</span> <span class="dl">"</span><span class="s2">scissors</span><span class="dl">"</span><span class="p">)</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span> <span class="o">++</span><span class="p">;</span>
    <span class="p">});</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">result</span><span class="dl">'</span><span class="p">,</span> <span class="nx">result</span><span class="p">);</span>

    <span class="new"><span class="kd">var</span> <span class="nx">winner</span><span class="p">;</span>
    <span class="k">if</span> <span class="p">(</span><span class="nx">result</span><span class="p">.</span><span class="nx">rock</span> <span class="o">&gt;</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span> <span class="o">&amp;&amp;</span> 
        <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span> <span class="o">&gt;=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span><span class="p">)</span> <span class="nx">winner</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">rock</span><span class="dl">"</span><span class="p">;</span>
    <span class="k">if</span> <span class="p">(</span><span class="nx">result</span><span class="p">.</span><span class="nx">paper</span> <span class="o">&gt;</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span> 
        <span class="o">&amp;&amp;</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span> <span class="o">&gt;=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span><span class="p">)</span> <span class="nx">winner</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">paper</span><span class="dl">"</span><span class="p">;</span>
    <span class="k">if</span> <span class="p">(</span><span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span> <span class="o">&gt;</span> <span class="nx">result</span><span class="p">.</span><span class="nx">rock</span> 
        <span class="o">&amp;&amp;</span> <span class="nx">result</span><span class="p">.</span><span class="nx">scissors</span> <span class="o">&gt;=</span> <span class="nx">result</span><span class="p">.</span><span class="nx">paper</span><span class="p">)</span> <span class="nx">winner</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">scissors</span><span class="dl">"</span><span class="p">;</span>

    <span class="k">if</span> <span class="p">(</span><span class="nx">winner</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
            <span class="k">if</span> <span class="p">(</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">!=</span> <span class="nx">winner</span><span class="p">)</span> <span class="nx">p</span><span class="p">.</span><span class="nx">lost</span> <span class="o">=</span> <span class="kc">true</span><span class="p">;</span>
        <span class="p">})</span>
    <span class="p">}</span>

    <span class="nx">setTimeout</span><span class="p">(()</span> <span class="o">=&gt;</span> <span class="k">this</span><span class="p">.</span><span class="nx">reset</span><span class="p">(),</span> <span class="mi">3000</span><span class="p">);</span></span>
    <span class="k">break</span><span class="p">;</span>
</code></pre></div></div>

The last line of that code above will automatically call a restart function after 3 seconds to start the next round. Let's add the reset function to our `gamemanager.js`.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code>            <span class="nx">setTimeout</span><span class="p">(()</span> <span class="o">=&gt;</span> <span class="k">this</span><span class="p">.</span><span class="nx">reset</span><span class="p">(),</span> <span class="mi">3000</span><span class="p">);</span>
            <span class="k">break</span><span class="p">;</span>
        <span class="p">}</span>
    <span class="p">}</span>

    <span class="new"><span class="nx">reset</span><span class="p">()</span> <span class="p">{</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">=</span> <span class="kc">null</span><span class="p">);</span>
        <span class="k">if</span> <span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">filter</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">lost</span><span class="p">).</span><span class="nx">length</span> <span class="o">==</span> <span class="mi">1</span><span class="p">)</span> <span class="p">{</span>
            <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">winner</span><span class="dl">'</span><span class="p">,</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">);</span>
            <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="nx">p</span><span class="p">.</span><span class="nx">lost</span> <span class="o">=</span> <span class="kc">false</span><span class="p">);</span>
        <span class="p">}</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">setState</span><span class="p">(</span><span class="dl">"</span><span class="s2">betting</span><span class="dl">"</span><span class="p">);</span>
    <span class="p">}</span></span>
<span class="p">}</span>
</code></pre></div></div>

We need to properly reset our game and controller screens as well. Update the reset event listener in `game.js` like this so that we can remove the results from the screen.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">reset</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">playerList</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">playerList</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="new"><span class="nx">players</span><span class="p">[</span><span class="nx">p</span><span class="p">.</span><span class="nx">id</span><span class="p">].</span><span class="nx">classList</span><span class="p">.</span><span class="nx">remove</span><span class="p">(</span><span class="dl">'</span><span class="s1">active</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">rock</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">paper</span><span class="dl">'</span><span class="p">,</span> 
            <span class="dl">'</span><span class="s1">scissors</span><span class="dl">'</span><span class="p">);</span></span>
    <span class="p">});</span>

    <span class="new"><span class="nx">rockValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">paperValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> 
        <span class="nx">scissorsValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="dl">""</span><span class="p">;</span></span>
<span class="p">})</span>
</code></pre></div></div>

Also add a listener to the reset event in our `controller.js` to reset the hand buttons.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">reset</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">()</span> <span class="p">{</span>
    <span class="nx">hand</span><span class="p">.</span><span class="nx">classList</span><span class="p">.</span><span class="nx">remove</span><span class="p">(</span><span class="dl">'</span><span class="s1">active</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">rock</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">paper</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">scissors</span><span class="dl">'</span><span class="p">);</span>
<span class="p">})</span></span>
</code></pre></div></div>

Now the game will automatically start a new round. But players that have lost are still able to play. Update the hand event listener inside our `gamemanager.js` so that it ignores these players.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">hand</span><span class="dl">'</span><span class="p">,</span> <span class="p">(</span><span class="nx">hand</span><span class="p">)</span> <span class="o">=&gt;</span> <span class="p">{</span>
    <span class="new"><span class="k">if</span> <span class="p">(</span><span class="nx">player</span><span class="p">.</span><span class="nx">lost</span><span class="p">)</span> <span class="k">return</span><span class="p">;</span></span>

    <span class="nx">player</span><span class="p">.</span><span class="nx">hand</span> <span class="o">=</span> <span class="nx">hand</span><span class="p">;</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">ready</span><span class="dl">'</span><span class="p">,</span> <span class="nx">player</span><span class="p">);</span>

    <span class="new"><span class="kd">var</span> <span class="nx">unready</span> <span class="o">=</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">find</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">&amp;&amp;</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">lost</span><span class="p">);</span></span>
    <span class="k">if</span> <span class="p">(</span><span class="o">!</span><span class="nx">unready</span><span class="p">)</span> <span class="k">this</span><span class="p">.</span><span class="nx">setState</span><span class="p">(</span><span class="dl">"</span><span class="s2">resolving</span><span class="dl">"</span><span class="p">);</span>
<span class="p">});</span>
</code></pre></div></div>

Now we can ignore players that have lost in the next round. Let's also visualize this on the game screen. We can pass the list of players in the reset function. The reset event is called at the beginning of each round so now we can change the icons to a different icon when a player has lost.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">reset</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">playerList</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">playerList</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nx">players</span><span class="p">[</span><span class="nx">p</span><span class="p">.</span><span class="nx">id</span><span class="p">].</span><span class="nx">classList</span><span class="p">.</span><span class="nx">remove</span><span class="p">(</span><span class="dl">'</span><span class="s1">active</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">rock</span><span class="dl">'</span><span class="p">,</span> <span class="dl">'</span><span class="s1">paper</span><span class="dl">'</span><span class="p">,</span> 
            <span class="dl">'</span><span class="s1">scissors</span><span class="dl">'</span><span class="p">);</span>
        <span class="new"><span class="nx">players</span><span class="p">[</span><span class="nx">p</span><span class="p">.</span><span class="nx">id</span><span class="p">].</span><span class="nx">classList</span><span class="p">.</span><span class="nx">toggle</span><span class="p">(</span><span class="dl">'</span><span class="s1">lost</span><span class="dl">'</span><span class="p">,</span> <span class="nx">p</span><span class="p">.</span><span class="nx">lost</span><span class="p">);</span></span>
    <span class="p">});</span>

    <span class="nx">rockValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="nx">paperValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> 
        <span class="nx">scissorsValue</span><span class="p">.</span><span class="nx">innerText</span> <span class="o">=</span> <span class="dl">""</span><span class="p">;</span>
<span class="p">})</span>
</code></pre></div></div>

![Round results]({{site.baseurl}}/images/results2.png)

## The final winner

Once there is only one player left we can declare our final winner. To do this let's check the player count in the reset function. If there is only player left that has not lost we can do a big reset where we set everyone's lost state to false again. Let's also emit an event to the host so we can display the winner in our game screen.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="nx">reset</span><span class="p">()</span> <span class="p">{</span>
    <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="nx">p</span><span class="p">.</span><span class="nx">hand</span> <span class="o">=</span> <span class="kc">null</span><span class="p">);</span>
    <span class="new"><span class="k">if</span> <span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">filter</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">lost</span><span class="p">).</span><span class="nx">length</span> <span class="o">==</span> <span class="mi">1</span><span class="p">)</span> <span class="p">{</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">room</span><span class="p">.</span><span class="nx">to</span><span class="p">(</span><span class="k">this</span><span class="p">.</span><span class="nx">host</span><span class="p">).</span><span class="nx">emit</span><span class="p">(</span><span class="dl">'</span><span class="s1">winner</span><span class="dl">'</span><span class="p">,</span> <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">);</span>
        <span class="k">this</span><span class="p">.</span><span class="nx">players</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="nx">p</span><span class="p">.</span><span class="nx">lost</span> <span class="o">=</span> <span class="kc">false</span><span class="p">);</span>
    <span class="p">}</span></span>
    <span class="k">this</span><span class="p">.</span><span class="nx">setState</span><span class="p">(</span><span class="dl">"</span><span class="s2">betting</span><span class="dl">"</span><span class="p">);</span>
<span class="p">}</span>
</code></pre></div></div>

Finally add the last bit of code to `game.js` to give our winner a nice golden hand in the next game.

<div class="language-js highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="new"><span class="nx">socket</span><span class="p">.</span><span class="nx">on</span><span class="p">(</span><span class="dl">'</span><span class="s1">winner</span><span class="dl">'</span><span class="p">,</span> <span class="kd">function</span><span class="p">(</span><span class="nx">results</span><span class="p">)</span> <span class="p">{</span>
    <span class="nx">results</span><span class="p">.</span><span class="nx">forEach</span><span class="p">(</span><span class="nx">p</span> <span class="o">=&gt;</span> <span class="p">{</span>
        <span class="nx">players</span><span class="p">[</span><span class="nx">p</span><span class="p">.</span><span class="nx">id</span><span class="p">].</span><span class="nx">classList</span><span class="p">.</span><span class="nx">toggle</span><span class="p">(</span><span class="dl">'</span><span class="s1">winner</span><span class="dl">'</span><span class="p">,</span> <span class="o">!</span><span class="nx">p</span><span class="p">.</span><span class="nx">lost</span><span class="p">)</span>
    <span class="p">});</span>
<span class="p">});</span></span>
</code></pre></div></div>

![Winner]({{site.baseurl}}/images/winner.png)

[View code](https://github.com/RubenBimmel/MultiplayerGameTutorial/tree/master/07-Gameplay)

## Wrapping up

That's it for this tutorial. We created a simple multiplayer game with a shared screen. It is a very simple game but the important thing is that we have learned the basics of setting up a server for a game like this. Now you should have enough knowledge to develop your own party games!

Thank you for reading my posts. If you have any questions you can leave them in the comments below or mail me at <a href="mailto:contact@rubenbimmel.nl">contact@rubenbimmel.nl</a>.