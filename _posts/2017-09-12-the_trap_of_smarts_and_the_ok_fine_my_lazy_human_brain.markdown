---
layout: post
title:  "My lazy, human brain"
date:   2017-09-12 15:21:57 -0400
---

A number of bad habits that I didn't know I had have become very apparent to me while learning programming. Foremost among them is my tendency to jump straight to solution mode. 

I'm naturally pretty good at problem solving, so I tend to just crash in head-first and just start working. Often, the challenge miraculously gets solved with very little actual thinking on my part. This works great most of the time... some of the time.

What I'm *not* good at is patience. Stopping, carefully analyzing the problem, and breaking it down into small pieces does not come naturally. Obviously, this skill is pretty core to success in an engineering role, and my weakness in this area has cost me many a debugging hour. 

A recent exercise was a frustrating kick-in-the-butt to improve my approach to new challenges. The triggering lesson focused on iterating through nested hashes, then modifying the data you pull out to be visually presented in various ways. 

I tend to be able to clearly visualize the structure of the hash, and I have a fairly solid understanding of how to access the level and element that I want to surface, so these sorts of challenges are usually pretty quick for me to solve. 

Unfortunately, this also means that I can be intellectually lazy about the problem, and just start typing away without thinking. Fortunately for me, Flatiron's curriculum mixes up the challenges, so lazy sacks like me can't get away with that. 😅

Here's the challenge I faced:

*Iterate through holidayhash and print the items such that your readout resembles:*
   
  ```
	Winter:
     Christmas: Lights, Wreath
     New Years: Party Hats
  Summer:
     Fourth Of July: Fireworks, BBQ
		 ...
	```
 
And so on for the formating of the remaining holidays.

Breaking it down, I started with a symbol and I needed to return a string, capitalized, and with a ":" after it. I didn't really stop to think through through the difference between where I was and where I needed to get, and so got very bogged down.

Where I eventually sloppily ended up: 

```

decorations = []

def all_supplies_in_holidays(holiday_hash)

  holiday_hash.each do |season, holidays|
	
    decorations << season.to_s.capitalize + ":"
      holidays.each do |holiday, details|
				holiday = holiday.to_s.capitalize + ": "
        holiday << details.to_s
      decorations << holiday
    end
	end
  puts decorations.flatten

```

Ick. Also, it didn't work.	

<blockquote class="imgur-embed-pub" lang="en" data-id="6pImDY8"><a href="//imgur.com/6pImDY8">View post on imgur.com</a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

The correct solution:
	
```

def all_supplies_in_holidays(holiday_hash)
  holiday_hash.each do |season, season_holidays|
    puts "#{season.to_s.capitalize}:"

    season_holidays.each do |holiday, decorations|
      holiday_string = holiday.to_s

      puts "  #{holiday_string.split("_").map {|el| el.capitalize}.join(" ")}: #{decorations.join(", ")}"
    end
  end
end

```

So much better!

I had all of this knowledge at my fingertips and could definitely have solved this wtih a little thought but, I got too bogged down in starting to code using what first came to mind. Whoops.

To try and force myself to follow better habits, I'm working on a process to walk through before I ever poke out a line of code. I try follow these steps every time, even if I 'know' how to do what I need to do. Behold!

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

So far, it's helped... most of the time. I still fall into my lazy habits if I'm tired or if I'm absolutely confident that I know what I'm doing (which is a warning sign in itself 😱) but I'm getting better!

Any other strategies to force yourself to slow down and evaluate before you start?
