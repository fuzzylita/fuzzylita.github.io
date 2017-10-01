---
layout: post
title:  "A CRUDdy Walkthrough"
date:   2017-10-01 21:46:12 +0000
---


The CRUD lab in the Sinatra secton of Flatiron's curriculum where we coded out the complete CRUD functionality, all in one go, for the first time was extraordinarily difficult for me. In addition the inner voice putting me down and telling me that I wasn't cut out for this, and that 'anyone else' would just get it (thanks a lot brain for making it worse), the logic was just not clear to me at all. 

This walkthrough goes through each reqest, what it means, where the data is going, and what's happening at each step. Hopefully this post will both cement my knowledge for my own sake, and perhaps help others cement theirs. 

First, if you feel like you've hit the wall that proves you're not cut out for this path in life, I want to tell you: This lab kinda sucks. You're smart, you're capable and if you're having a hard time getting through this, at least one other person out there had a really hard time putting it all together.


Let's go through the steps, and what is happening along the way:

**1. Create**

```
The user makes a GET request to '/posts/new' by entering the address into their browser's navigation bar.
Our controller processes that request by rendering the content at new.erb

  get '/posts/new' do
    erb :new
  end
	
	
That content consists of a form that submits a POST request to '/posts'

<form action="/posts" method="POST">
  Post Title: <input type="text" name="name" id="name">
  Post Content: <input type="text" name="content" id="content">
  <input type="submit" id="submit">
</form>

When the controller receives a post request to '/posts' it responds by creating a new Post object (defined in the Models). This object inherits from ActiveRecord::Base, giving it access to all of the ActiveRecord methods. 

The new Post is created using ActiveRecord methods and the param values for Post.name and Post.content, which were defined by the form entries. It then concludes by showing the user the complete list of existing posts (using the ActiveRecord method .all). You could just as easily build the response to display the single post if you like, I just happened to build it this way.

post '/posts' do
    @post = Post.create(params)
    @posts = Post.all
    erb :index
  end
```

**2. Read**

```
If the controller receives a GET request to '/posts'. It responds by rendering the code defined in index.erb (below). This code displays all of the existing posts to the user (using the AR .all method).

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

The controller can also respond to a GET request for a specific post at '/posts/:id'. It responds to this by first finding the appropriate post object using the ActiveRecord method Model.find with the same id from the POST request. The id id set in the params hash when the post is created according to the **C**reate workflow above. Next, the response renders the code defined in show.erb (below). This code displays the specific post to the user.

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
