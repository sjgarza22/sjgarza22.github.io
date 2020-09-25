---
layout: post
title:      "Rails Project: TravelR"
date:       2020-09-25 00:18:53 +0000
permalink:  rails_project_travelr
---


For my Rails project, I decided to make a Travel Planning app. The idea came about when last month a friend and I were planning a camping trip. I love to plan when it comes to travelling. Whenever I find something cool for us to do on a trip I usually message her the idea. The frustrating part is that when I want to revisit any of the ideas I sent her, I have to scroll back through all of the messages we've sent to eachother between them. So annoying.

This is why I created TravelR. It allows people to plan a trip and keep all of their ideas in one centralized location. This makes planning a trip both easy and convienent.

## The Plan

![Lucid Chard - TravelR](https://thumbnails-photos.amazon.com/v1/thumbnail/zedFi2p5T0KJTGf3HdC_4g?viewBox=736%2C913&ownerId=AR9HY2RVVET8H)

As you can see from the above chart, I planned quite a bit of functionality for my app. I decided for posterity to focus on the User (this includes Trip_Users), Trips and the Idea Board (including Ideas). Since I had everything so neetly planned, it was time to start coding.

## Getting it to Work

When I started my project, I began by building out my Database migrations, creating my models and started defining their relationships. Once that was done, I focused on fleshing out my User role. I finished my New form, I created my controllers and I successfully was able to create new users. This is, of course, the point when I decide to do something completely different.

#### Devise

I decided that I wanted to use Devise and the main reason was I had heard from a classmate that it made setting up OAuth easier (and who doesn't like easy?), which meant that all the work I had just done was pointless. I ended up having to trash everything I created for my users. I had to rollback my migrations, delete my Create_Users migration, and clear my session cookies (be sure to do this before deleting anything, because this actually caused a big issue).

With the help of my co-hort lead--who provided a very, handy-dandy cheat sheet--I was able to get Devise up and running. After that, getting my OAuth's set up was very simple.

#### OAuth

For my OAuth login's, I wanted to use GitHub, Facebook, and Google. I had heard that latter two were hard to get working, but with Devise I found the process fairly simple. Here's how it works:

###### Gemfile

You're going to need to add the following gems to your Gemfile:

1. dotenv-rails
2. omniauth-github
3. omniauth-facebook
4. omniauth-google-oauth2

That first gem is absolutely nessessary, but you only need the omniauth gems for the OAuth's you're going to use. All you need to do now is run **bundle install** and it's time for the next step.

###### .ENV File

If you've worked with .env files and know exactly what do to, feel free to skip this section. For those of you who haven't I will give a brief explanation as to what this is, why we're using it, and the steps you need to take to create it.

A .env file is a file that is a hidden file that we use to store sensitive information needed for our program to run. In this case, we're going to use it to store our OAuth authentication tokens. If you were using an API, this is also were you would store any authentication tokens needed to access that API.

1. Create a new .env file in your root folder.
2. Create variable holders for your tokens.
    (These will be all caps, and you should follow normal Ruby naming conventions. ex. GITHUB_KEY)
3. Add your tokens and set them to the appropriate variable

Now that you have this done, you're going to...

###### Devise Config

Now that we've gotten that done. It's time to get Devise configured to use OAuth, and the first thing you're going to want to do is create a new migration.

```
rails g migration AddOmniauthToUsers provider:index uid:index
```

Run rake db:migrate and this will add the nesscessary columns we need to add to Devise's User model.

Next, we need to go into config/initializers/devise.rb and scroll down until you get to the omniAuth section. We need to add the following line:

```
config.omniauth :facebook, ENV['FACEBOOK_KEY'], ENV['FACEBOOK_SECRET']
```

I used Facebook in this example, but you can change it to whichever OAuth service you decided to use.

With this taken care of, it's time to wrap everything up in our Controllers.

Let's add the following code to our ApplicationController:

```
protect_from_forgery with: :exception
before_action :authenticate_user!
```

What this is going to do, is prevent users from being able to access the sections of our site that they should be logged in for. Which is why the next piece of code is important:

```
skip_before_action :authenticate_user!, :only => [:index]
```

If you have a landing page, or anything that you want a user to be able to access when they aren't logged in, you need to add this code to the controller with that page(s). You can specifiy a specific route you want them to access, or if the controller has several routes you want them to be able to access, you and always delete the only at the end.

Next up is adding a controller to handle the omniauth callbacks. In Controllers, you're going to want to create a new folder called **users**.

In that folder, create a new file called omniauth_callbacks_controller.rb, and add the following code:

```
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def facebook
     @user = User.from_omniauth(request.env["omniauth.auth"])
     sign_in_and_redirect @user      
  end
end
```

For every OAuth you use, you're going to need to define a route method like above. Now for the finial part, let's go back into our ApplicationController and you're going to create a new private method:

```
def after_sign_in_path_for(resource)
    request.env['omniauth.origin'] || root_path
end
```

What this piece of code does, is redirect the user to the root_path after they are signed in, but you can change that path to any you would like. For me, I had them redirected to their Trips#Show page.

That's it. Now when you go to your login/sign-up page, you should see a Sign-in Link with your chosen OAuth provider.

## Conclusion
This project was challenging, but I had a lot of fun learning new things and putting what we learned in our Rails mod all together. My project isn't perfect, but I'm still really proud of it. For those who are soon going to be working on their own Rails project, my one piece of advise is to have a plan of action before you get started. Make sure if you're even thinking of using Devise, that you set that up first before you really start to code.

