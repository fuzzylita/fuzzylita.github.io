---
layout: post
title:      "Off the Rails "
date:       2017-11-13 14:01:46 -0500
permalink:  off_the_rails
---


Wow. Ok, so Rails.

At risk of repeating myself, I definitely didn't feel confident going into this project. I know, I know, go back to any of the other sections and you'll see me say the same thing. Blah blah, wasn't prepared, project was a breeze, learned so much, whatever. Not quite so successful this time around.

Rails was the third, and by *far* the densest section of the Flatiron curriculum. Rails is behemoth, taking years of professional work to really master, and I get that. Still there were some requirements for this project that were awfully chewy to get through, as the content of the section had to be compacted for sanity and timely completion. Frankly, certain functionality in Rails is not very well documented, and required much keyboard bashing and hope. 

The requirements for the final project were fairly straightforward, but extensive, covering the basics of a rudimentary, but complete and fully functional web application. Having done fairly well on the curriculum for most of the Rails section, most requiremetns were not too bad.

There were only two real ringers, which I had struggled with throughout the later curriculum:
* Had to include a join table with a writeable property, that could be writtent to through a complex nested form
* The nested form had to write to multiple models, taking advantage of the object_attributes= methods.

I decided to recreate and expand on my sinatra project, just built on Rails, with improvements and increased functionality.
The app is a fairly simple cocktail database. 

Users can sign up, and create cocktails recipes for their own account. Users have complete control over the recipes that they own, with the ability to edit and delete recipes as they wish. In addition, users can view a public page of all recipes in the database, but cannot edit them. Users can add ingredients, but can't delete them, as ingredients can be used by all recipes.

My model relationships were set up as follows:
* A user can have many drinks
* A drink belongs to a user, and has many drink ingredients (my join table), and has many ingredients through drink ingredients.
* An ingredient has many drink ingredients, and has many drinks through drink ingredients.
* Drink ingredients belong to a drink, belong to an ingredient, and have a custom attribute, quantity. This is set when you create a drink from the new drink form.

The actual architecture of the app could be taken directly from any basic rails tutorial. I followed conventions of MVC architecture, with each of my models (User, Drink, Ingredient and Drink Ingredients) set up with the appropriate routes, controller actions and views to support the user workflow. 

Rather than spending 8 paragraphs describing the project in detail, a visual should give you a pretty good sense of how it works in a general way.

<blockquote class="imgur-embed-pub" lang="en" data-id="49xdn7X"><a href="//imgur.com/49xdn7X"></a></blockquote><script async src="//s.imgur.com/min/embed.js" charset="utf-8"></script>

The 'interesting' challenge really came with trying to build out the complex form, and honestly this is where I both learned the most, and almost quit forever. I'll focus on this for the remainder of this post.

In the case of my application, users will interact with the nested form when they either create a new, or edit an existing cocktail. The form is a partial, separated out with some very basic logic to show appropriate fields for ingredients based on the action the user is taking. Let's take a look at the code. There's some fomatting and code to display the partial with the apporopriate locals, but I'll leave it for simplicity:

The form for a new drink uses Rails' form_for helper to wrap around a Drink object and generate HTML fields with the appropriate syntax to create these drink objects automatically. You can see I'm taking advantage of a custom URL with drink.id as the last parameter, so that whether I'm editing or creating a new drink, I can use the same form.

```
    <%= form_for @drink, :url => "/users/#{current_user.id}/drinks/#{@drink.id}" do |f| %>
      <%= f.label :name %>
      <%= f.text_field :name %> <br>
```

If there's a drink ID, then we know we're editing an existing drink, so I can display the page a little differently. We want the user to see the ingredients that they've already selected, as well as an option to add additional ingredients.

The code below iterates through each ingredient already associated with a drink, displaying them for the user along with their quantity. The syntax for this selection box was remarkably difficult, and I had to consult with multiple rails developers in order to get it right. It took several very long days of trial and error to get it built out and functional, and between you and me, I'm not sure if I'd be able to rebuild it without a ittle more, but it certainly was a learning experience! Additionally, (and much less complex) the user will see a single field to add a new ingredient and quantity, if they wish to expand on their existing drink.

Notice, all of the names of these fields are hard coded appropriately for us to interact with ingredients and drink-ingredient quantities within the same form. We'll build out ingredients= and ingredients_attributes= methods within our drink model. This enables our drink instantiate new objects of these two kinds. 

```
"drink[ingredients][<%= index %>][quantity]"
"drink[ingredients_attributes][0][id]" 
etc.
```

```
    <% if @drink.id %>
      <h2>Select an Ingredient</h2>
      <% @drink.ingredients.each.with_index do |ingredient, index| %>
        <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-', :selected => ingredient.id}, :name => "drink[ingredients][#{index}][id]" %>
          <label>Quantity</label>
          <input type="text" name="drink[ingredients][<%= index %>][quantity]" value="<%= find_quantity @drink, ingredient %>"><br>
      <% end %>		

      <br><h2>Add Ingredients</h2><br>
      <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-'}, :name => "drink[ingredients_attributes][0][id]" %>
          <label>Quantity</label>
          <input type="text" name="drink[ingredients_attributes][0][quantity]"><br>
```

If there is no drink ID, we know we're dealing with a brand new drink, so we want to give the users all of the available ingredients to choose from, in the same dropdown form we've used above. This time, we'll give the users 5 optional ingredients, notice that the key that would be autogenerated by the has_nested_attributes has been hard coded for the five fields. 

```
    <% else %>
    <br><h2>Add a new Ingredient</h2><br>
      <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-'}, :name => "drink[ingredients_attributes][0][id]" %>
          <label>Quantity</label>
          <input type="text" name="drink[ingredients_attributes][0][quantity]"><br>
      <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-'}, :name => "drink[ingredients_attributes][1][id]" %>
        <label>Quantity</label>
        <input type="text" name="drink[ingredients_attributes][1][quantity]"><br>
      <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-'}, :name => "drink[ingredients_attributes][2][id]" %>
        <label>Quantity</label>
        <input type="text" name="drink[ingredients_attributes][[2][quantity]"><br>
      <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-'}, :name => "drink[ingredients_attributes][3][id]" %>
        <label>Quantity</label>
        <input type="text" name="drink[ingredients_attributes][3][quantity]"><br>
      <%= f.select :ingredients, Ingredient.all.collect {|x| [x.name, x.id]}, {:include_blank => '-'}, :name => "drink[ingredients_attributes][4][id]" %>
        <label>Quantity</label>
        <input type="text" name="drink[ingredients_attributes][4][quantity]"><br>
      <br><br>
    <% end %>

      <%= f.label :instructions %>
      <%= f.text_area :instructions %> 

      <%= f.submit class: "btn btn-primary" %>
    <% end %>
```

Of course, the setup of the form is only part of the challenge. We also need to handle the creation of the objects within the Drink model. Rails supports interaction with different objects from within a model using has_nested_attributes_for, which will automatically create a object_attributes= method. For our purposes however, we'll need to interact with two different models, and in a rather custom way, so we'll override this and create out own methods.

```
   def ingredients_attributes=(ingredients_attributes)
    ingredients_attributes.each do |key, value|
      next if value[:id].blank?
      
      ingredient = Ingredient.find_by(:id => value[:id])

      self.ingredients << ingredient

      dr_ing = self.drink_ingredients.last
      dr_ing.quantity = value[:quantity]
      dr_ing.save
      self.save
    end
  end

  def ingredients=(ingredients)
    #{"0"=>{"id"=>"5", "quantity"=>"2"}, "1"=>{"id"=>"7", "quantity"=>""}, "2"=>{"id"=>"3", "quantity"=>""}}
    
    # This is now a static array. We need this to keep track of the originial state of the drink
    # otherwise we'd modify the array as you iterate. Bad.
    original_ingredients = self.ingredients.to_ary

    ingredients.each do |ing_idx, ingredient_object|
      original_ing = original_ingredients[ing_idx.to_i]
      
      # If the user sets the selection field to '-', we will remove the ing
      # from the drink.
      if ingredient_object[:id] == ""
        self.ingredients.delete(original_ing)
        next
      end

      # We need to see if we need to update the ingredient, or just the quantity
      # If the original ingredient ID at the index in the hash matches the ID within
      # the hash at that index, they're the same and you just need to update quantity

      if original_ing.id == ingredient_object[:id].to_i
        dr_ing = DrinkIngredient.find_by(
          :drink_id => self.id, 
          :ingredient_id => original_ing.id
          )
        dr_ing.quantity = ingredient_object[:quantity]
        dr_ing.save
        next
      end

      # If the user has changed the original ingredient, we need to add it, 
      # delete the old one, and set the quantity
      
      self.ingredients.delete(original_ing)
      self.ingredients << Ingredient.find(ingredient_object[:id].to_i)
      dr_ing = DrinkIngredient.find_by(
        :drink_id => self.id, 
        :ingredient_id => ingredient_object[:id].to_i
        )
      dr_ing.quantity = ingredient_object[:quantity]
      dr_ing.save
      self.save
    end
```

