---
layout: post
title:      "Sinatra Project Success! I really needed that."
date:       2017-10-10 02:20:56 -0400
permalink:  sinatra_project_success_i_really_needed_that
---


I ... struggled with the SQL/ORM/Rack/Sinatra section of the Flatiron program. I bogged down badly in several parts, and many of the last labs were multiple day affairs. 

There were tears.

<iframe src="https://giphy.com/embed/8w68TkeqzDnLa" width="480" height="350" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/sad-crying-sailor-moon-8w68TkeqzDnLa"></a></p>

I'm behind on my timeline, and was both rushed and intimidated going in, especially after my slow pace with the last few labs. Thankfully, I quickly came around when I actually started the project. Similar to my CLI data gem, I had the tremendous good fortune to have a pretty solid idea going in, and one that aligned nicely with the project requirements.

I decided to build a collaborative cocktail database. Essentially a public recipe book for cocktail ingredients and instructions. Users signup, and - when logged in (the pain of validating user logins came later ... I blame Rack::Flash) - could access both their own cocktail creations, and a public list of all drinks and drink ingredients submitted by their fellow users.

The project was a relative breeze, and I genuinely enjoyed making my app. I'm pretty proud of the outcome!

Here are the steps that I went through to complete this project:

1. Holy bananas, let me tell you about Corneal. Whoever made this project is a saint. One gem install later, and I had the complete framework in place, including gems, placeholders for my migrations, the complete file tree and all of the associated Model/View/Controller folders for every table. EVEN THE INHERITENCE OMGGGG. CRUD in a box.
2. My plan for my app was very closely aligned with the project structure and relationships we build out in the earlier, (and BRUTALLY PAINFUL) playlister project, but as a result, the workflow for mine was quite intuitive. All in all, it took me about 12 horus to complete. ... probably less than playlister :}
3. the actual structure of the app was quite simple, I only found that I bogged down when I got into some trouble with Flash 
