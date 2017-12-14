---
layout: post
title:      "At Least I'm Consistent (A wordy breakdown of a jQuery refactor)"
date:       2017-12-05 14:39:35 -0500
permalink:  js_project_review
---

I'm not doing it this time. I'm not starting out my blog with the same paragraph, expressing my lack of confidence in my familiarity with the material, not going to talk about how it came together surprisingly and I retained more than I thought. If you want to know, you can read the first paragraph of [any](http://litastephenson.com/2017/09/15/bringing_it_all_together/) of my [other](https://learn.co/blog/blog_posts/8623/edit) project [posts](http://litastephenson.com/off_the_rails). Have a good time, it goes like this:

* OH HOLY HELL. I KNOW NOTHING
* I'M GOING TO COMPLETELY FAIL AT THIS PROJECT, THIS IS IT. THIS IS THE END
* OH IT'S DONE.

Wooooo. 🍺

For the majority of you who religiously read my blog posts and keep up to date on my latest and greatest challenges, you'll have a solid recollection of the Rails project we were tasked with. It was a challenging undertaking, with fairly extensive requirements. If you're interested in reliving the thrilling tale, or for the one or two of you who may not have read it several times already, you can find details in the last of the links above.

By comparison, the requirements for our JavaScript section project were fairly lightweight, but I'll explain each of them in tedious detail to make up for it.

We were tasked with taking our existing Rails application and refactoring it to have a jQuery frontend. I'll sketch out each reqeuirement, and how I addressed it below.

*** THE DETAILS ***

# Requirement 1:
## Must translate the JSON responses into Javascript Model Objects, with at least one method on the prototype. 
I'll explain this requirement first, as it was necessary to complete any of the other project requirements. The core of the goal here was that we could not simply make a request, and render data directly from the response to the DOM. Instead, the response to a request (in JSON) should be used to instantiate new Objects, and we should use properties of these newly instantiated objects to determine the content on the screen.

To allow my existing Rails models to be represented in JSON, I used Rails' Active Model Serializer gem, which is explicitly part of the requirement below. This gem allows you to selectively convert properties of your models into JSON. In preparation for the rest of the requirements, I created a Drink Object constructor using the class syntax, although it could have been done through a constructor function instead. 

```javascript
// This is the ES6 class declaration, which will allow us to instantiate new drink objects (with new Drink(json)) and the attributes and methods that each new object will inherit.

class Drink {
  constructor(drink) {
    this.id = drink.id
    this.name = drink.name
    this.instructions = drink.instructions
    this.user_id = drink.user_id
    this.created_at = new Date(drink.created_at)
    this.username = drink.username
    this.unformatted_rating = drink.rating
    this.ingredients = drink.ingredients
  }

  // this method just shows the date more nicely in the index page
  formatted_date() {
    return this.created_at.toLocaleDateString()
  }

  // this method is a custom getter that allows the JS obj to display a more
  // user friendly message. It could have been done in the template, but I felt it was
  // more closely related to the state of the object, so made sense to include
  get rating() {
    if (this.unformatted_rating === null) {
      return "Not Rated Yet!"
    }
    return this.unformatted_rating
  }
}
```

# Requirement 2:
## Must render at least one index page via jQuery and an Active Model Serialization JSON Backend. 

I addressed this requirement in three places in my application, due to the structure of my Rails app. I have three separate index pages. I show a user an index of their own drinks, I allow users to view a public view of all of the drinks, where they can see creation/creator information. I also show an index page for the ingredients available for the drinks. All were refactored to update via jQuery. I will specifically discuss the public index page.

I updated the action in my controller to respond to requests for both JSON and HTML. This means that a user can make standard get requests by navigating the application URLs normally. The user can also explicitly request JSON by adding .json to the end of the URL:

```javascript
  def all
    respond_to do |format|
      format.html
      format.json { 
        @drinks = Drink.order_by_name   
        render json: @drinks
      }
    end
  end
```

When a user navigates to the /drinks page, in order to view the public drinks list, my router will make a get request to this path, and this will be handled in my controller by the all action above.

this will load the HTML as normal, but you'll see a distinct difference here: 

```javascript
<% provide(:title, "All Drinks") %>

<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <div>
        <ul id="drinks_list">
        </ul>
      </div>
      <%= link_to "Create a new Drink",  new_user_drink_path(current_user), class: "btn btn-sm btn-primary" %>
      <%= link_to "Go Back",  user_path(current_user), class: "btn btn-sm btn-primary" %>
    </section>
  </aside>
</div>

<script>
  $(function() {
    getDrinks("<%= drinks_path %>")
  })
</script>
```

Note that the drinks list has been removed, with only a ul tag with an id of 'drinks_list' remaining.

You'll also note a script tag has been added at the bottom of the HTML. Upon page load (the jQuery function wrapping my method is shorthand for $(document).ready(function())) the contained function will execute, which will load our drinks list.

```javascript
// we append the drinks list to an empty ul which is sitting on the page.
function getDrinks(path) {
  $.getJSON(path, (resp) => {
    if (resp === null ) {
      $("#drinks_list").append(`
      <h1>There are no drinks!</h1>
      `)
    }

    resp.forEach(function(item) {
      let drink = new Drink(item)
      $("#drinks_list").append(`
      <li>
        <strong>${linkTo(drink.name, `${path}/${drink.id}`)}</strong><br>
        Created by ${drink.username} on ${drink.formatted_date()}<br>
        Rating: ${drink.rating}
      </li><br>
      `)
    })
  })
}
```

getDrinks will call a jQuery function, making a get request explicitly for a JSON response to the path which we pass in from the ERB tag in our HTML file. This is handy, as it brings the path into scope not only for our get request but also for other functions within getDrinks.

If the response body is empty, you'll see I account for this by showing a more user friendly message, instead of just writing null to the page. assuming that's not the case, I'll handle the response. The response will be an array of JSON objects. I iterate through each of these, and instantiate a new drink object using our handy constructor described above. the value of let will only persist for the duration of each iteration, so it works fine to set each new object to drink, and then handle rendering its data. 

Once I have the Drink object, I simply append a string to our empty UL by id via jQuery. You'll see I'm using the properties of my object to display drink information, as well as taking advantage of the path that was brought into scope to handle creating the link to the show page. I use a helper method called linkTo, to avoid repeating code on the page.

```javascript
function linkTo(name, path) {
  return `<a href="${path}">${name}</a>`
}
```

# Requirement 3:
## Must render at least one show page via jQuery and an Active Model Serialization JSON Backend.
Similar to rendering the index page, I have several different show pages, a public show page where a user can rate a drink created by any user, a private show page where the user can edit their own drink and a show page which shows drinks filtered by my scope method.

I will discuss the public show page. 
I updated the action in my controller very similarly, and like the index page, the html has been updated to an empty div, to which I will append the rendered Drink data. Let's take a look at how that drink data is displayed. It's a long one!

```javascript

function getDrink(path) {
  $.getJSON(path, (resp) => {
    if (resp === null ) {
      $("#drink_info").append(`
      <h1>Drink does not exist!</h1>
      `)
    }

    let drink = new Drink(resp)
    $("#drink_info").append(`
      <h1>
        ${drink.name}!
      </h1>
    
    // This comes from an external library which will allow me to convert my numerical drink rating into a star count.
    // It was a fun addition to play with after meeting the requirements
    
      Drink Rating:
      <input id="current_rating" type="text" class="rating" data-size="xs"><br>
    
    // You'll note, within my string, I add another string. This is a bit sneaky. I have to render a list of ingredients
    // within the show page, and it was a nightmare to include that looping code within the string. Instead, I added a
    // ul which I can access with jQuery
    
      <p><strong>Ingredients: </strong></p>
      <ul id="ingredients_list">
      </ul>

      <p><strong>Instructions: </strong></p>
      <ul>
        <li>${drink.instructions}</li>
      </ul>
      <br>
    `)

    // **** EXPERT MODE ****
    
    // This comes from the star_rating library to translate the rating visually into stars. 
    // This syntax below comes from the documentation for that library. 
    // First line is a configuration setting specifed, second is the command that sets the display val.
    // 'rating' here is not related to the rating for the drink, the library has a custom jQuery method
    // on elements 

    // displays uneditable current rating on the screen
    $("#current_rating").rating("refresh", {displayOnly: true, showCaption: false, showClear: false});
    $("#current_rating").rating("update", drink.rating)

    // the template is created above, now I'm going to append each ingredient as an li to that list
    
    let ing = $("#ingredients_list")

    drink.ingredients.forEach(function(el) {
      ing.append(`
      <li>${el.name} - ${el.quantity}</li>      
      `)
    })

    // only the public view should show the message about making a drink a proper cocktail
    if (drink.ingredients.length === 3 && path.includes("users") === false) {
      ing.append(`
      <li>Add bitters to make a proper cocktail!</li>
      `)
    }
  })
}

```

# Requirement 4:
## The rails API must reveal at least one has-many relationship in the JSON that is then rendered to the page.
My Drink object has many drink_ingredients. Active Model serializer allows me to define the has_many relationship, which can then be represented in the JSON.

```ruby
class DrinkSerializer < ActiveModel::Serializer
  attributes :id, :name, :instructions, :user_id, :created_at

  # This custom attribute allows us to access the creator's username from wthin drink

  attribute :username do
    object.user.username
  end

  # same for username

  attribute :rating do
    object.ratings.average(:rating)
  end

  # we access quantity through drink_ing, but this sets the field in JSON to be
  # 'ingredients' that we can pull the quantity from. (drink ingredients has both ingredient)
  # data and quantity data

  has_many :drink_ingredients, key: "ingredients"
end
```

In my project this was covered by the ingredients to the drink, which I then render in the iteration through the ingredients list shown above.

# Requirement 5:
## Must use your Rails API and a form to create a resource and render the response without a page refresh.

I address this requirement in two places, to a lesser extent, I address this within the drink creation page. In my rails app, I had five new ingredient fields so that the user could create a drink as large as they like. that's not very JS'y however. In my application, I updated the performance of this page so that there is one field on the page when you render, but you can add as many new fields as you wish by clicking the 'add another ingredient' button.

On my partial for the new and edit drink pages, you'll see that the form contains a single field as before for a new ingredient by default. Notice the two divs surrounding the selector. There is an ingredient selector surrounding the selection box and quantity field, and a second div called the ingredient appender. 

```javascript
      <br>
      <h2>Add a new Ingredient</h2><br>
      
      <div id="ingredient-appender">
        <div class="ingredient-selector">
          <%= f.select :ingredients, Ingredient.formatted_list, {:include_blank => '-'}, :name => "drink[ingredients_attributes][][id]" %>
          <label>Quantity</label>
          <input type="text" name="drink[ingredients_attributes][][quantity]"><br>
        </div>
      </div>
      <br>
 ```
Immediately below, you'll see an anchor tag, which calls the addIngredientFields() function contained within our drinks.js file.

```javascript
      <a href="#" id="new-ingredient" class="btn btn-primary" onclick="addIngredientFields()">Add Another Ingredient</a><br><br>

// from drinks.js

function addIngredientFields() {
  let newFields = $(".ingredient-selector")[0].cloneNode(true)
  newFields.children[0].value = ""
  newFields.children[2].value = ""
  $("#ingredient-appender").append(newFields)
}
```

This code looks complicated but it's actually quite simple! I found a method online (cloneNode(true)) which will make a copy of all of the code contained within an html tag on the page. I simply grab the first ingredient selector (the one originally on the page) with jQuery, copy it, and then clear the fields (when you copy it, the text comes along with it, so you need to remove it). Finally I just append the new, cleared form to the appender div.

The second approach to handling this requirement is the dynamic creation of new ingredients, but I'll leave that for another post :).
