---
layout: post
title:  "The trap of 'smarts' and the (ok fine, *my*) lazy human brain"
date:   2017-09-12 19:21:56 +0000
---

One of the most challenging habits that I've discovered about myself while learning programming is my tendency to jump straight to solution mode. 

I'm naturally pretty good at problem solving, so I don't tend to really think through what I have, and what I need to accomplish. I just crash in head first and just start working, and the challenge miraculously gets solved. 

What I'm *not* naturally good at is stopping, analyzing the problem, and then breaking it down into small pieces. Obviously, this skill is pretty core to success in an engineering role, and my weakness is becoming increasingly apparent. 

A recent exercise was a painful push in the right direction to start improving my habits. The lesson focused on iterating through nested hashes. I tend to be able to clearly visualize the structure of the hash, and I have a fairly solid understanding of how to access the level and element that I want to surface, so these sorts of challenges are usually pretty quick for me to solve. 

Unfortunately, this also means that I can be intellectually lazy about the problem, and just start whacking away at code, without really thinking. Fortunately for me, Flatiron's curriculum mixes up the focuses challenges so lazy sacks like me can't get away with that. 😅

Here's the challenge:

Iterate through holiday_hash and print the items such that your readout resembles:
   
  Winter:
     Christmas: Lights, Wreath
     New Years: Party Hats
  Summer:
     Fourth Of July: Fireworks, BBQ
  etc, etc for the format of the remaining holidays.

I started with a symbol. The message in the tests said I needed to return a string, capitalized, and with a ":" after it. I didn't really stop to think through through the difference between where I was and where I needed to get, and so got very bogged down.

Where I eventually sloppily ended up: 

```decorations = []

def all_supplies_in_holidays(holiday_hash)

  holiday_hash.each do |season, holidays|
	
    decorations << season.to_s.capitalize + ":"
      holidays.each do |holiday, details|
				holiday = holiday.to_s.capitalize + ": "
        holiday << details.to_s
      decorations << holiday
    end
	end
  puts decorations.flatten```

Ick. Also, it didn't work.	
The correct solution:
	
```def all_supplies_in_holidays(holiday_hash)
  holiday_hash.each do |season, season_holidays|
    puts "#{season.to_s.capitalize}:"

    season_holidays.each do |holiday, decorations|
      holiday_string = holiday.to_s

      puts "  #{holiday_string.split("_").map {|el| el.capitalize}.join(" ")}: #{decorations.join(", ")}"
    end
  end
end```

So much better!

I had all of this knowledge at my fingertips and could definitely have moved forward but, I got too bogged down in trying to solve the problem in exactly the same way as usual, largely due to the fact that I didn't really think it through.

To try and brute-force better habits, I'm working on a process to go through before I ever poke out a line of code. Ideally I will follow these steps every time, even if I 'know' how to do what I need to do. Behold!


**MY NOT-SO-GROUNDBREAKING STRATEGY FOR NOT BEING A LAZY PROGRAMMER**

* Read the description completely, *twice*
* Start with quick notes on how you plan your approach: 
	* What do I need to return? 
	* What do I have to work with?
	* What do need to do to what I have to get it to match the intended outcome?
* Write pseudo-code for your approach
* Walk through the pseudo-code once manually to make sure that you understand the returns at each step
* Replace the pseudo-code with real code
* Optimize the real code

So far, it's helped a lot. I still fall into my lazy habits if I'm tired or if I'm absolutely confident that I know what I'm doing (which is a warning sign in itself 😱) but I'm getting better!

Any other strategies to force yourself to slow down and evaluate before you start?
