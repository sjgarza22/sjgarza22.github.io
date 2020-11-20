---
layout: post
title:      "Haunts: JS FrontEnd / Rails Backend Project"
date:       2020-11-20 05:46:35 +0000
permalink:  haunts_js_frontend_rails_backend_project
---

My Mod 4 project was completely inspired by my best friend. Her idea was for an app that would help you locate nearby paranormal sites. I've been interested in the paranormal since I was a kid, so for me, this was the perfect project. The biggest challenge was going to be adding search fuctionality. Luckily, I found the perfect Gem to help out with that: Geocoder. The issue: getting it to work.

![Laugh Crying Gif](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2Fmedia.giphy.com%2Fmedia%2FoOX5qIDkzDjeo%2Fgiphy.gif&f=1&nofb=1http://)

## FrontEnd vs. BackEnd Search
Depending on your project, this may be a question that occurs to you. It really depends on your project and the amount of data that you will be working with. If you're thinking of having an index page where you'll pull all your data to put on display, then I would suggest using JavaScript for your search. It will be much faster than using a backend search. If you're like me, and you end up have a large dataset to work with, then a backend search is going to be the best for you.

For my project, I ended up finding a dataset online that had been scraped from a website that collected information about paranormal locations. I will say, if I were ever to host this application publicly, I will probably not be using this dataset, but I decided to use it for development purposes because I knew at some point the application would be searching through a large set of data and this set would be great for testing purposes as it was all geocoded already.

## Ruby Geocoder
This was the first time I used this Gem, just as I'm sure those of you who come accross my blog may also be using it for the first time. While Geocoder has documentation, I had a hard time navigating it. It doesn't exactly have a Setup Guide, which is why I will be writing one as I would prefer no one have to go through the pain I did of getting Geocoder up and running.

### Install the Gem
This part should be a no-brainer, but if you haven't already, add the Geocoder gem to your Gemfile and run 'bundle install'.

For my setup, I really only need one additional gem: dotenv. You may or may not need this gem, it really just depends on how you configure Geocoder which we will be going over next.

**NOTE**: For this setup, we will not go over configureing to Cache our geocoding search results. This may be something I'll go over on a later date (once I figure it out...), but for now, we just want to get this app up and running as quickly as possible.

### Configuration File
To start, we're going to need to create the configuration file. Go to config/initializers and create a new file inside this folder named: geocoder.rb

Once your file is created, go ahead and copy the following code:

```
# config/initializers/geocoder.rb
Geocoder.configure(

    # street address geocoding service (default :nominatim)
    lookup: :nominatim,
  
    # IP address geocoding service (default :ipinfo_io)
    ip_lookup: :ipinfo_io,
  
    # to use an API key:
    #api_key: ,
  
    # geocoding service request timeout, in seconds (default 3):
    timeout: 5,
  
    # set default units to miles:
    units: :mi 
  )
	```
	
There are more configuration options, but for development and for the purposes of this project, this is really all we need. Now, let's go over what's going on in this file.

The first option we're configuring is for our geocoding service. Geocoder is not an actual geocoding service, but provides us with easy to use methods to geocode/reverse-geocode location data, and it gives us the search method: near(location, distance).

In this configuration file we're using the default geocoding service: Nominatim. Nominatim is a complete free service and we don't need an api key to access it. It does limit us to only one api call per second. Great for development purposes, but if we ever deploy this app, we are going to have to change this. If you would like to learn more about which Geocoding Services you can use, click [here](https://github.com/alexreisner/geocoder/blob/master/README_API_GUIDE.md).

Again, for IP address geocoding, we're going with the default. This was not something I ever utilized in my project, but I decided to leave it, as it is something I would be interested to use for my app in the future.

The next option is for your API key, this is where the dotenv comes into play. You don't want anyone to get hands on your API key, so if you use a service other than Nominatim, but sure to install the dotenv gem and setup your dotenv file.

The timeout option is there to disconnect us from the geocoding service if it's taking too long to respond. The last option allows us to set which unit of measure we want to use: kilometers(km) or miles(mi). Since I'm in the states, I configured geocoder to use miles, but can change that to kilometers is you would prefer. If you don't set units option, it will default to km.

### Model
We're going to need to setup the Model that we'll be using Geocoder with. You can see what that looks like below.

```
class HauntsLocation < ActiveRecord::Base
  extend Geocoder::Model::ActiveRecord
  
  reverse_geocoded_by :latitude, :longitude
  after_validation :reverse_geocode
end
```

In Rails when we create our Models, they inherit from ApplicationRecord, unfortunately, Geocoder did not take kindly to this. I kept getting: **ArgumentError (Unsupported argument type: 0 (Integer))** and it wasn't until I changed my model to inherit directly from ActiveRecord::Base that it started working correctly. You can still get the error above, but now it only happens if our geocoding service can't find the location we entered into our Near search. I found this out when I just happened to misspell the name of a City.

You need to extend Geocoder in your model. The next two lines allows our model to automatically geocode our data. I used reverse_geocoded_by, because the dataset I was using was already geocoded. If I decide to add an address field, when I pull the data it will reverse-geocode it and add the results to that address field. Right now, I havne't added the entirity of my dataset, like I mentioned, it's very large and I haven't figured out a way to add it without it crashing, but the next line will do that same upon creation of any new HauntsLocation.

This is a topic the documentation goes over really well, and to figure out how to configure your model, I'd take a gander over [here](https://github.com/alexreisner/geocoder#geocoding-objects).

## Conclusion
I hope this guide will be useful to anyone looking to use the Geocoder gem in their project. It took me a long time to get this working, but out of all our other projects, this is the first one that really feels legit. Definitely looking forward to our last mod and seeing what I will get to build next.

