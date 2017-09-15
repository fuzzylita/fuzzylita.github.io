---
layout: post
title:  "Bringing it all together"
date:   2017-09-15 18:28:47 +0000
---


The creation of my cocktail CLI app. Working great so far!

when I wanted to code originally, this is the app I wanted to make, so the idea side was a piece of cake.
It was a little tough to find an API that had the data that I wanted. It was then a challenge to figure out how to use this api, as it had no documentation.

A little googling and some helpful friends in the industry got me there though.

Never built an app from scratch. It went much faster than I expected. Honestly fatster than the assesssments, probably because I was interested in the outcome.

Here was my process, derived from the tutorial video:

1. use bundle gem <name>
2. create the executable file first and test it
	- this will require setting the permissions properly, which I had no clue how to do yet. Now I do 
	- chmod -x file - this means that you're changing the permissions to allow it to be executable so that you can run the file
3. build the CLI controller with all of the user interaction logic
4. make sure you have all of the necessary dependencies set up
5. make a mock of the logic, using static data. this means the only thing that will need to change is to add in the scraped data later
6. once it's all there, hook into the api
7. perfect it
8. go!
