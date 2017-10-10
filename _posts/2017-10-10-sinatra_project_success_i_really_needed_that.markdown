---
layout: post
title:      "The Joy, The Pain, The Tears... The ... SQL to Sinatra section"
date:       2017-10-10 02:20:56 -0400
permalink:  sinatra_project_success_i_really_needed_that
---


The struggle was real with the SQL/ORM/Rack/Sinatra section of the Flatiron program. 
I bogged down badly in several parts, and many of the last labs were multiple day affairs. 

There were tears.

<iframe src="https://giphy.com/embed/8w68TkeqzDnLa" width="480" height="350" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/sad-crying-sailor-moon-8w68TkeqzDnLa"></a></p>

Aggressive though it may be, I'm behind on my timeline, not at all helped by my... optimistic expectations of this section. I was both rushed and discouraged going in to the final project, after my slow pace with the last few labs. (PLAYLISTER, I'M LOOKIN' AT YOU, BUDDY.)

Thankfully, the project was a genuine relief. Similar to my CLI data gem, I had the tremendous good fortune of a solid, well defined idea going in, and one that aligned easily with the project requirements.

We were tasked with building a simple content management system which must:
  1. Follow the Sinatra MVC structure
  2. Demonstrate complex(ish) relationships between objects
  3. Support all standard CRUD functionality
  4. Support some form of data validation
  5. Support some basic permissions-based functionality
  6. Support secure (heavy air-quotes) user Login/Logout

In keeping with the theme of my first project (and my life), I decided to build a collaborative cocktail database.

In essence, it's a simple, public recipe book for cocktails, created by "you" (me) and "your fellow users" (.. also me ..). 
Users sign up, and - when logged in - can access and manipulate their own cocktail creations, and have view permissions to a complete public list of drinks and ingredients submitted by "fellow users" (me).

Honestly, I was in *the dark place* when I went into the project, but I finished with a renewed energy, and belief that I'm following the right path in pursuing engineering professionally.

Blah blah blah. Here's what I did:

1. First and foremost, holy bananas; whoever made [Corneal](https://github.com/thebrianemory/corneal) is a saint. It also makes me think of eye anatomy. I can't help it. Anyway, one gem install later, and you're looking at a complete framework for my project in place. Gems, migrations, complete file tree, all of the associated Model/View/Controller folders, everything, everything. CRUD in a box. There's not much credit to give myself for this. Push button, receive 95% of the project done for you. I hear this is how software engineering works, by the by.
2. My plan for my app was very closely aligned with the project structure and relationships we build out in the earlier, (and BRUTALLY PAINFUL) playlister project, but as a result, the workflow for mine was quite intuitive. All in all, it took me about 12 horus to complete. ... probably less than playlister :}
3. the actual structure of the app was quite simple, I only found that I bogged down when I got into some trouble with Flash 
