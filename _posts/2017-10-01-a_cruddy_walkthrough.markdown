---
layout: post
title:  "A CRUDdy Walkthrough"
date:   2017-10-01 17:46:13 -0400
---


The CRUD lab in the Sinatra secton of Flatiron's curriculum where we coded out the complete CRUD functionality, all in one go, for the first time was extraordinarily difficult for me. In addition the inner voice putting me down and telling me that I wasn't cut out for this, and that 'anyone else' would just get it (thanks a lot brain for making it worse), the logic was just not clear to me at all. 

This walkthrough goes through each reqest, what it means, where the data is going, and what's happening at each step. Hopefully this post will both cement my knowledge for my own sake, and perhaps help others cement theirs. 

First, if you feel like you've hit the wall that proves you're not cut out for this path in life, I want to tell you: This lab kinda sucks. You're smart, you're capable and if you're having a hard time getting through this, at least one other person out there had a really hard time putting it all together.


Let's go through the steps, and what is happening along the way:

**1. Create**


The user makes a GET request to '/posts/new' by entering the address into their browser's navigation bar.
Our controller processes that request by rendering the content at new.erb

```
  get '/posts/new' do
    erb :new
  end
```
	
	
That content consists of a form that submits a POST request to '/posts'

```
<form action="/posts" method="POST">
  Post Title: <input type="text" name="name" id="name">
  Post Content: <input type="text" name="content" id="content">
  <input type="submit" id="submit">
</form>
```

When the controller receives a post request to '/posts' it responds by creating a new Post object (defined in the Models). This object inherits from ActiveRecord::Base, giving it access to all of the ActiveRecord methods. 

The new Post is created using ActiveRecord methods and the param values for Post.name and Post.content, which were defined by the form entries. It then concludes by showing the user the complete list of existing posts (using the ActiveRecord method .all). You could just as easily build the response to display the single post if you like, I just happened to build it this way.

```
post '/posts' do
    @post = Post.create(params)
    @posts = Post.all
    erb :index
  end
```

**2. Read**


If the controller receives a GET request to '/posts'. It responds by rendering the code defined in index.erb (below). This code displays all of the existing posts to the user (using the AR (ActiveRecord) .all method). Notice that I am iterating through the array of Post objects and displaying the information for each, again using AR methods.

```
  get '/posts' do
    @posts = Post.all
    erb :index
  end

	<h1>Current Posts</h1>

<% @posts.each do |post| %>
<h2>Post number <%= post.id %></h2>
<h2>Title: </h2> <%= post.name %><br>
<h2>Content: </h2> <%= post.content %><br>
<% end %>
```

The controller can also respond to a GET request for a specific post at '/posts/:id'. It responds to this by first finding the appropriate post object using the ActiveRecord method Model.find with the same id from the POST request, and setting the object that is returned instance variable. This will allow us to work with that object. The ID is set in the params hash when the post is created, according to the **C**reate workflow above. Next, the response renders the code defined in show.erb (below). This code displays the specific post to the user.

```
  get '/posts/:id' do
    @post = Post.find(params[:id])
    erb :show
  end
	
	<h1>Post number <%= @post.id %></h1>

<h2>Title: </h2> <%= @post.name %><br>
<h2>Content: </h2> <%= @post.content %><br>

<form action="/posts/<%= @post.id %>/delete" method="post">
  <input id="hidden" type="hidden" name="_method" value="delete">
  <input type="submit" id="delete" value="Delete">
</form>
```

You'll notice, there's another form in there. That form is going to allow our users to delete the post, and we'll get to that in just a moment. First, let's take a look at how a user can perform the **U** in our CRUD methods, updating an existing Post object.

**3. Update**

Up until now things were going okay for me, but this section got really hairy, really quickly, and I hit a confidence and progress wall. I won't go into what I did wrong or where I got bogged down (Too soon, man. Too soon.) but I will go through what's happening in the working solution below. 

We know that the user will want to edit an individual post, so we'll start by setting up our controller to support updates. With the following code, the controller can respond to GET requests to ''/posts/:id/edit'. Note that we again create an instance variable @post, which points to the correct Post object, via the AR .find method for the correct ID. We can again work with the parameters for that object.

```
  get '/posts/:id/edit' do
    @post = Post.find(params[:id])
    erb :edit
  end
```

The edit form  below will submit a PATCH request to '/posts/:id/. Personally I found this very confusing, as the code for a patch request is hard to understand at first. (It's also hard to find simple documentation. If you find a good source for beginners, let me know!) 
	
Anyway, a patch request allows you to update individual fields in an object (or resource, as I see it defined sometimes.) 
	
To make a patch request, you must update your config.ru with Sinatra middleware which intercepts and examines each request sent. If it finds a request containing 'name: `_method', it will customize the request type, replacing 'post' with whatever request type you enter for 'value' in the line containing _method. This is what supports the patch request type. 

```
	use Rack::MethodOverride 
	
	<form action="/posts/<%= @post.id %>" method="post">
  <input id="hidden" type="hidden" name="_method" value="patch">
  Edit Post Title: <input type="text" name="name" id="name" value="<%= @post.name %>">
  Edit Post Content: <input type="text" name="content" id="content" value="<%= @post.content %>">
  <input type="submit" id="submit">
</form>
```

This form above will now submit a PATCH request to '/posts/id', and when received by our controller, it responds by executing the AR .update method on the Post object with the matching id. 

To make sense of the below code, remember the .update method syntax: Model.update(id, attributes)

```
  patch '/posts/:id' do
    Post.update(
      params[:id].to_i,
      name: params[:name],
      content: params[:content]
    )
    redirect "/posts/#{params[:id]}"
  end
```

Finally, we will conclude by redirecting to the correct path for the post we have updated. Remember, we're back in Ruby-land, so I interpolated with sring interpolation syntax.
	
We're nearly done.
	
**4. Delete**

This method was much more straightforward, but I was well tired and quite discouraged by this poing, so it was still difficult for me to figure out. Let's take a look:

We want to make a delete request. But how? And where should the user submit this request? Remember the form that we added to our show.erg file above? Now we'll dig into it and break it down.

Similar to our PATCH request, we'll take advantage of the MethodOverride middleware, which is looking for requests containing`_method. Here we have one, with a value set to delete. 

```
<form action="/posts/<%= @post.id %>/delete" method="post">
  <input id="hidden" type="hidden" name="_method" value="delete">
  <input type="submit" id="delete" value="Delete">
</form>
```

This will then change our request to be a DELETE request to '/posts/:id/detete'. The controller will respond by defining an instance variable @post set to the appropriate Post object (by AR .find), and deleting the post via the AR .delete method. Finally, we render our delete.erb file, which confirms for the user that we've deleted their post.

```
  delete '/posts/:id/delete' do
    @post = Post.find(params[:id])
    @post.delete
    erb :delete
  end
```


