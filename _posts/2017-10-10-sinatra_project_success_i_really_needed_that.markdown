---
layout: post
title:      "The Joy, the Pain, the Tears... The SQL to Sinatra Section"
date:       2017-10-10 02:20:56 -0400
permalink:  sinatra_project_success_i_really_needed_that
---


The struggle was real with the SQL/ORM/Rack/Sinatra section of the Flatiron program. 
I bogged down badly in several parts, and many of the last labs were multiple day affairs. 

There were tears.

<iframe src="https://giphy.com/embed/8w68TkeqzDnLa" width="480" height="350" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/sad-crying-sailor-moon-8w68TkeqzDnLa"></a></p>

Aggressive as it is, I'm behind on my timeline, not at all helped by my... optimism for my pace through this section. I was both rushed and discouraged going in to the final project, after my hideous pace in the last few labs. (PLAYLISTER, I'M LOOKIN' AT YOU, BUDDY.)

Thankfully, the project was a genuine relief. Similar to my first project, I had the tremendous good fortune of going in with a well-defined idea of an app I'd want to (ok, do) use myself, and was able to align it with the project requirements.

We were tasked with building a simple content management system which must:
  1. Follow the Sinatra MVC structure
  2. Demonstrate complex(ish) relationships between objects
  3. Support all standard CRUD functionality
  4. Support some form of data validation
  5. Support some basic permissions-based functionality
  6. Support secure (heavy air-quotes) user Login/Logout

In keeping with the theme of my first project/life, I decided to build a collaborative cocktail database.

In essence, it's a simple, public recipe book for cocktails, created by "you" (me) and "your fellow users" (... me). 
Users sign up, and - when logged in - can access and manipulate their own cocktail creations, and have view permissions to a complete public list of drinks and ingredients submitted by "fellow users" (me).

I was definitely in *the dark place* when I went into the project, but I finished with a renewed energy, and belief that I'm following the right path in pursuing engineering professionally.

Blah blah blah. Here's what I did:

First and foremost, let's be frank; whoever made [Corneal](https://github.com/thebrianemory/corneal) basically did my project for me (and makes me think of the anatomy of the eye). One gem install, a couple pokes of setup, and you've got yourself all of the tedious parts of your project, done. Gems, migrations, environment setup, complete file tree, all of the associated Model/View/Controller folders, everything, everything. CRUD in a box. There's not much credit to give myself for this. Push button, receive 95% of your project done for you. I'd feel bad about this, but I'm told this is how software engineering works.

Anyway! Here's the broad strokes of the project (that I didn't miraculously receive from a gem):
* The data structure and object relationships were pretty simple to set up:
	* A user has many cocktails
	* A cocktail has many ingredients
	* An ingredient has many cocktails
	* A cocktail belongs to a user
* Next to build out the details. My plan for my app was mercifully aligned with the project structure and relationships built out in the earlier - painful - playlister project. As a result however, the workflow for project was fairly well ingrained, and I was able to use the lab instructions for loose guidelines when I got stuck on what steps to take next. It also helped my workflow have a little bit more structure, rather than bouncing from one thing to the next
	* User creates an account/can log in
	* A logged in user can:
		* Create drinks
		* Create ingredients
		* View their drinks
		* View public drinks
		* View public ingredients
		* Update their drinks
		* Delete their drinks
	* CRUD!

The actual structure of the app was quite simple, and preventing users from editing drinks that don't belong to them, as well as validating the entries they submit was quick and easy, I only found some trouble when approaching alerts for bad logins with Rack::Flash.

This experience can be best summarized with: 'What **-bleep bleep-** is **-bleep-** and **-bleep bleep bleep-** ever **-bleep bleep-** AND I JUST CAN'T ANYMORE.'

It seems that there was a problem with using this system of notification for any of my routes involving user validation. My setup was correct, and I get notifications just fine from other requests in the app. Somehow, once you get to user logins, it all goes to pot. While the structure of the requests were no different, the messages just... didn't... work. I spent several hours investigating in earnest myself, and then recruited some more experienced eyes, in the hopes that it was just ignorance in its finest form.

After googling, whining, receiving help, my genrous help whining, and all of us finally determining that everything is [not a nail after all](https://en.wikipedia.org/wiki/Law_of_the_instrument), I simply solved my alert system with several hybrid notification systems and a great deal of *%#*!@ and #%*&$@% *EVEN AFTER I *!#@&*%.

And the project was done! A good time was had by all (me).

TL;DR - Use [Corneal](https://github.com/thebrianemory/corneal).
