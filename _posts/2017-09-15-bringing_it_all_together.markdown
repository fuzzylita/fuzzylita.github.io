---
layout: post
title:  "Cocktail APIs for the masses!"
date:   2017-09-15 14:28:47 -0400
---


I confess, I was intimidated to start the CLI gem project, the self-driven conclusion to our first section at Flatiron School. I was nervous that I may not have internalized the material well enough to succeed in making my first gem from scratch, without a code-along.

Fortunately, since the moment I decided that I wanted to learn to code originally, I knew I wanted to make a cocktail library, so the idea aspect of the project was a piece of cake. 

My gem would need to present the user with a list of base liquors, then based on their selection, present a list of drinks that use that base. The user should select again from the resulting drinks list, and then must be presented with a list of ingredients, and instructions on how to create their drink of choice. 

Based on some of the available guidance from the program materials, I outlined my plan of action:

1. use bundle gem <gem name> to create the skeleton
2. create the executable file first and test it
	- this will require setting the permissions properly
	- chmod -x file - this means that you're changing the permissions to allow it to be executable so that you can run the file
3. build the CLI controller with all of the user interaction logic
4. make sure you have all of the necessary dependencies set up
5. make a mock of the logic, using static data. this means the only thing that will need to change is to add in the scraped data later
6. once it's all there, hook into the api
7. perfect it
8. go!

With a well described map of my project in hand, my next challenge was to determine the best source for the the data I'd be working with, so that I could build out the skeleton of my project to fit seamlessly with minimal refactoring. 

I looked around quite a bit, and scraping a website for the cocktail data did not seem to be an efficient strategy to surface the options to the user. I decided to try to find an appropriate public database with an API instead. It was a little tough to find a source for the data that I wanted, but eventually I found [the cocktail db](http://www.thecocktaildb.com/api.php), which looked like it would fit the bill.

Plan, and data model decided on, I began to design and optimize the user workflow, dialing in just what information would need to be surfaced at what time, and ensuring that I'd be covering edge cases. To make sure that the user-facing logic was correct and would behave as I intended, I created a hash to simulate the multiple calls for bases, drinks and recipes (which would eventually come from the API response). 

Completing the user experience with static data went surprisingly quickly, meaning that all I would need to do at the end is hook up the API, and make some small improvements to allow for its use. I completed the whole workflow in less than a day! This success really boosted my confidence to take on the API integration.

It was a good thing that I had that little confidence boost, as the next (and by far the biggest) challenge was to figure out how to use this api, as it had no documentation whatsoever, and the response returned a stringIO object, something that I'd never encountered and didn't know how to interact with. ...

<iframe src="https://giphy.com/embed/OURWV9iDaWb7O" width="480" height="358" frameBorder="0" class="giphy-embed" allowFullScreen></iframe><p><a href="https://giphy.com/gifs/falling-shocked-OURWV9iDaWb7O">via GIPHY</a></p>

Fortunately, some slightly frustrated googling and some extremely helpful friends in the industry helped to get me on the right track. 

With some console tinkering to figure out how to navigate the response, I eventually ended up with exactly what I needed out of the API:

```
 #this code returns an object which contains all of the drinks using that base
    base_results = open("http://www.thecocktaildb.com/api/json/v1/1/filter.php?i=#{base}")
		
    #this code breaks that object (StringIO) into a parsable hash, and then it returns a list of the names of the drinks 
		
    drink_names = []

    JSON.load(base_results)['drinks'].each do |k,v|
      k.each do |k, v|
        if k == "strDrink"
          drink_names << v
        end 
      end
    end
    drink_names
  end 

  def drink_components(drink)
    drink_results = open("http://www.thecocktaildb.com/api/json/v1/1/search.php?s=#{drink}")

    drink_details = JSON.load(drink_results)["drinks"].first.reject do |k,v|
      v == "" || v == " " || v == nil
    end
```

Once I'd figured out how to parse a stringIO object into a JSON file that I could read from, there was just a little bit of refinement to do with the data - removing empty strings and plugging in the keys where my hash had been. 

Within 3 days, my gem was done and ready to go! I finished it up with some extremely necessary ascii art to set the user's mood, and published the gem. 

```

                            /|     /|                            
                          // ( `""-                             
                        \\| /      .\\___                         
                        /        .        4                        
                       /              _ /                        
                      /            .--'                          
                     (              \\                          
                    /               \\ \\                        
                   /            \\ \\  \\                       
        ,         /              \\   \\ \\                       
        )\\      /                \\   \\                        
      \\/  |   .'`   _                \\           \\______/_/        
    \\.   /  .'       '.        ,\\ \\/               \\   (o)/         
  \\'   /   /          \\  ;    |   /                \\  ' /          
  \\'   \\  |           |  |    |  '                  \\  /           
    \\,   `" |           /.|   | |                    ||            
        ''-..-\\      _.;.'|   |.;-.                  ||            
              \\    <`.._ )) |  .;-.))               / \\           
              (__.  `  ))-' \\_    ))'              +++++          
                   `'--"`      `"""`

```

I'd never built an app from scratch and I was afraid I wouldn't be able to do it, but It went much faster than I expected. Feel free to gem install cocktail_library to play with it. Cheers! 


