---
layout: post
title:      "CLI Project: DND 5th Edition"
date:       2020-05-25 21:46:55 +0000
permalink:  cli_project_dnd_5th_edition
---


This time around I wanted to do something different with my project by using an API instead of scraping a website. The API I chose to use was the [DND 5e API](https://www.dnd5eapi.co/). The reson that I decided on an API this time was because I thought it would be easier to use especially when it came time to iterate through the data, but it didn't turn out that way.

The first time I watched Avi's video and followed it pretty closely. This time I watched my Cohort Leads' video walkthrough. Dominique used an API as well, and I watched only the first half of the first video to figure out how to set up my eniornment and my API class to grab the data that I wanted. At this point, I decided to strike out on my own.

## The Setup

Similar to my scaping project, I had two classes, one that initialized and ran the program (my CLI class), and the other which grabbed the data (my API class). Unlike my scraping project, I was receiving data from an API which is packaged in the form of JSON. To grab my data I had to require two gems:

```
require 'httparty' ## Interacting with API
require 'json' ## Parsing data from API in JSON
```

The first gem, HTTPARTY, is similiar to Nokogiri although unlike Nokogiri it doesn't require me to install Open-URI. Everything is packaged in one, so to load my data I just needed one line of code.

```
response = Net::HTTP.get(URI("#{BASE_URL}#{endpoint}")) # Load data
data = json_to_array(response) # Parse Data

# JSON Parse method
def self.json_to_array(data)
        JSON.parse(data)
end
```

The first line of code above is what grabs my data. I saved the API's base url as a constant in my API class, and then saved the Endpoints as constants in my individual Object Classes. The next line parsed my data. Ruby cannot read JSON data, so it had to be converted to an Array of Hashes. The best part of using an API was that I was easily able to go through the data and pick out what I needed.

## My Object Classes

I had four major object classes: PlayerClass, Race, Equipment, and Spells. I had five additional classes that inherited from the Equipment class, and those objects were: Weapon, Vehicle, AdventureGear, Tool, and Armor. I created these additional classes because each of these Equipment Types sometimes came with their own specialize Attributes that none of the others had. It was in this was that I was able to make each of them their own Create, Fill_Attributes, and Print methods. While I could have tried to keep everything in the Equipment class, those three methods I mentioned would have ended up being unnecessarily large. This way, things would be neater, and by inheriting from Equipment, it allowed me to keep things DRY.

```
class Vehicle < Equipment
    def self.create(name, url, description = nil, speed = nil)
        @speed = speed
        @description = description
        self.new(name, url)
    end

    def fill_attributes(data)
        @equipment_category = data["equipment_category"]
        @vehicle_category = data["vehicle_category"]

        if data.key?("speed")
            @speed = "#{data["speed"]["quantity"]} #{data["speed"]["unit"]}"
            @capacity = data["capacity"]
        end

        @cost = "#{data["cost"]["quantity"]} #{data["cost"]["unit"]}"
        @weight = data["weight"]
        if data.key?("desc")
            @description = data["desc"]
        end

    end

    def print
        puts "#{@name}\n\n"
        puts "Equipment Category: #{@equipment_category}\n\n"
        puts "Vehicle Category: #{@vehicle_category}\n\n"
        puts "Cost: #{@cost}\n\n"
        puts "Weight: #{@weight}\n\n"
        if @speed != nil
            puts "Speed: #{@speed}\n\n"
            puts "Capacity: #{@capacity}\n\n"
        end
        if @description != nil
            puts "Description: #{@description}\n\n"
        end
    end
end
```

Each of these items extended my Findable module, which allowed me to keep things DRY even further by having my find_by_name and find_or_create_by_name methods inside it which every one of these classes uses.

## In Conclusion

First off, I had an amazing time creating this CLI. It was both challenging and fun. I enjoyed being able to work with inheritence and modules. I would like to work on it even after this. I would add additional Objects and perhaps create a Character Generator so that people could create their own DND characters.
