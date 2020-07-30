---
layout: post
title:      "Sinatra Project: Muses"
date:       2020-07-30 23:31:03 +0000
permalink:  sinatra_project_muses
---


I built this web application with the idea of having a website built specifically for roleplaying. This has been something of a dream project for me. It's been an idea in my head for years, and I will probably continue working on it long after we graduate and see it to fruition if I can.

## The Setup
When I started this project, I began with imagining the file structure. I knew I was going to have at least 3 databases to begin with--Users, Posts and Muses--and with that in mind I assembled my MVC (Model-View-Controller) framework.

Controllers
  |
	|-application_controller.rb
	|-muses_controller.rb
	|-posts_controller.rb
	|-users_controller.rb
	|
Model
  |
  |-muse.rb
	|-post.rb
	|-user.rb
	|
Views
  |
	|-Muses
	   |
		 |-edit.erb
		 |-new.erb
		 |-show.erb
		 |
	|-Posts
	   |
		 |-edit.erb
		 |-new.erb
		 |-show.erb
		 |
 |-Users
    |
	  |-edit.erb
		|-login.erb
		|-show.erb
		|-signup.erb
		
I also set up 3 databases: Muses, Users and Posts.

## The Process
The first thing I did was go into my Models and add my belongs_to and has_many ActiveRecord functions. Both Muses and Posts belong_to User, and my User has_many Posts and Muses. Now, for this project, we had to implement CRUD or Create-Read-Update-Delete. So after my Models were done, I started writing out my controller routes and views for my User. My User can do the first three letters of CRUD, but not the last. At the moment, only Muse and Post fully impletment CRUD.

My Sign Up page was the first I wrote because it needs to come before the Read-Update-or Delete routes, which makes sense because you can't achieve those three without Creating an object and adding it to the database first.

```
get '/signup' do
  erb  :'signup'
end

post '/signup' do
  user = User.new(params)
	
	if User.save
	  session[:user_id] = user.id
		redirect '/dashboard'
	end
end
```

Create and Update require two routes, while Read and Delete only need one. When doing the Update routes it's very important to make sure only the specific User who's ID is attached to the object is the only doing the actual updating.

## Conclusion
This project was a lot of fun, and *holy moly* it really feels like I'm a programmer ya'll! It's such a big accomplishment getting to this point and it's very weird how quickly we got to this point. I look forward to learning Rails, my next project, and being able to write more complex applications.
