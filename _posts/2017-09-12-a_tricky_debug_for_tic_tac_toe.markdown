---
layout: post
title:      "A Tricky Debug for Tic Tac Toe "
date:       2017-09-12 18:50:55 -0400
permalink:  a_tricky_debug_for_tic_tac_toe
---


I was doing fairly well working on my first really complex challenge, a version of tic tac toe with CLI component for users to interact with the program, and an computer player with rudimentary AI. It's a good challenge, and we've iterated on the concept several times throughout my Flatiron program so far. 

I'd swiftly coded out all but the last few methods, which required the game methods - which control the gameplay and conclusion of the game - to directly interact with the board methods - which control the state and interactivity of the tic tac toe board.

Once I began to code the more complex code interactions I several walls, and it was quite a challenge to debug.  

The first problem I uncovered was with the .play method, written below. This method needs to cooperate with a turn method and cycle through turns until the game is over. Initially, didn't think carefully enough about the methods that I was calling and was trying to get it to work with:


```
def play
  if !over?
    turn
  end
end
```


I was befuddled as to why it was only running once, *until* I realized, WHOOPS, I'm an idiot...  'if, else, end'  isn't a looping method. derp. I changed to use UNTIL and broke through that wall. Solved several additional tests right away, but once I tried out: 


```
def play
  until over?
    turn
end 
```


I hit an infinite loop.

<blockquote class="imgur-embed-pub" lang="en" data-id="a/NXcHe"><a href="//imgur.com/NXcHe"></a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

My brain immediately:

<iframe src="https://giphy.com/embed/IBkWv4OLecliM" width="480" height="480" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/what-sailor-moon-wut-IBkWv4OLecliM"></a></p>

I struggled long and hard to discover what was happening, since the code looked ok. The path of the loop was very difficult to follow, but I ultimately (with much help) debugged the problem:

the .play method validates whether the .over? method returns true:


```
def over?
  @board.full? == true
end
```


```
def play
  until over?
    turn
end 
```


over? returns true if the board is full. 
If that's not the case, then the .play method calls .turn again (at least it does now, with the single loop problem resolved).


```
def turn
  input = current_player.move(current_player.token)
  
	if board.valid_move?(input)
    board.update(input, current_player)
  else
    turn
  end
end
```


For reference, the .move method here just asks for a numerical input, and saves it to a variable.
The cooperating board methods, .valid_move, .taken? and .update then determine the next step.


```
def taken?(input)
  position(input) == "X" || position(input) == "O"
end 


def valid_move?(input)
  input.to_i.between?(1,9) && taken?(input) == false
end 


def update(input, player)
  if valid_move?(input) == true
    @cells[input_to_index(input)] = player.token
  end 
end 
```

So, where on earth was my problem?

I 'pry'd the heck out of every method to try to figure out what was causing that loop to occur, and I just couldn't get it. Eventually I had to call on the help of my friendly local Ruby expert and we poked away until we uncovered the issue, which was actually coming from my .over? method!

Ultimately, my definition of .over?, which was only returning true if the board was full. The game was 'won', but the board was not full, so .over? continued to call .turn again and again. By pulling in .won? and .draw? to address all of the ways that a game can be ‘over’ in a single method, it fixed all of the tests.

The infinite loop was resolved.
My original code is commented out, but left for reference. 

```
def over?
  # @board.full? == true
  @board.full? || won? || draw?
end
```

``` 
def draw?
  # over? && won? == false
  @board.full? && won? == false
end
```

Anyone hit a similar problem?


