---
layout: post
title:      "CLI Data Gem: NYT Bestsellers List"
date:       2020-02-18 05:24:29 +0000
permalink:  cli_data_gem_nyt_bestsellers_list
---


For my data gem I decided to scrape the New York Times' Bestsellers List, or rather, lists. The purpose of this project was to demonstrate not only my ability to scrape data from a website, but mostly my understanding and ability to create a Object Oriented Program (OOP). The NYT Bestsellers list is broken down by 3 genres: Fiction, Nonfiction, and Children's. Further, the lists for each Genre is broken down further based on print media. Accross these three genre's there are a total of 11 lists. My Gem captures these lists and then allows the user to select any list which will display all current titles with their corresponding author(s) and summary.

 When I first began writing my program, I followed Avi's video for Daily Deals pretty closely, until eventually, I needed to branch out. I had made up my mind pretty early that I was going to have a Book and Author class that would have a has-many relationship. These two objects would also be collaborating with my Lists class. Since we had written similar code in earlier labs, I was able to reuse a lot of code to create these objects.
 
  From the start, it was clear the my program was pretty complicated, but I made progress with "fake" data as Avi called it, until it was time to start using real data from the website. That's when things got messy. The first few bits of data I scraped didn't impede me too much, but when it came time to scrape the all of the book data, I hit a wall.
	
	I kept trying to scrape a certain attribute called: itemprop
	
	Why? Because each itemprop value equaled one of the items I wanted to scrape, thus I thought this would be easy.
	
	I was wrong.
	
	```
	title = doc.css("meta[itemprop=name]").first.text
	#With this code I was able to scrap an itemprop item, but not the one I wanted
	```
	
	I struggled for over an hour trying to find a way to scrape my data using the itemprop attribute.
	
	Luckily, my cohort lead was able to give me the push I needed to get past this hurdle.
	
	```
	data = list_doc.search("article.css-1u6k25n").each do |d|
                    book = Book.find_or_create_by_doc(d)
                    @list.add_book = book
  end
	```
	
	
Searching the HTML for the Article's with that class allowed me to grabbed the data I needed. It is also better for my program in the log run. Should the NYT's website go under any major changles it will be a lot easier to update my Gem than it would have been had I been able to use the itemprop attribute.

Then came my next challege: Getting the data I actually wanted.

Getting the title was easy. The first h3 element was the title, but the rest of the info was in p tags and there were p tags I didn't want to grab. There was also the fact that some books appeared in more than one list. How was I going to grab this data, and how do I make sure there wasn't already a Book or Author object already created?

```
# Say hello to my find_or_create_from_doc method
def self.find_or_create_by_doc(doc)
        if self.find_by_name(doc.search("h3").text.strip) != nil
            book = self.find_by_name(doc.search("h3").text.strip)
        else
            book = self.new(doc.search("h3").text.strip)
            count = 0
            doc.search("p").each do |content|
                case count
                when 1
                    book.author_name = content.text.strip
                when 2
                    book.publisher = content.text.strip
                when 3
                    book.summary = content.text.strip
                end

                count += 1
            end
        end
        book
end
```
 
What this method does is it will check if a book is already created, and if not, it will create a new Book Object. If you remember the artist_name method from the Collaborating Objects Lab, this method is the same. It will check to see if an Author object with the given name exists, and if not, it will create a new Author object.

My case object may not be perfect, and likely that will change possibly to an if statement later, but as I looked through the website code I could see there was only one p tag before the p tags that I actually wanted to scrape, so that is how I decided to grab that information.


P.S. If you happen upon my blog, let me leave you with a few words of wisdom that my 8th Grade Math teacher imparted to our class on his first day on the job in the middle of the year (long-story-short: old math teacher got a new job last minute).

DON'T WORRY.

If you're a geek like me, those words will impart you with images of towels, babbel fish, and thumbs pointing up to the sky to catch a ride to the next galaxy. Unfortunately for the rest of you, you will have no such feelings of nostalgia to comfort you but those two words are still good advice. I wasted so much time worrying about writing my program that I did not finish it until the last minute. Start. Write. Get frustrated? Go to your cohort and ask questions. Then take a breather, but don't let fear stop you. Before you know it, you'll be having fun and the program will be finished.

P.P.S. If they still have ya'll watching Avi's video and you happen upon my blog...

```
puts <<~DOC
text
text
text
DOC
```

Using <<~ instead of <<- with DOC gets rid of the white space in front of the text.

Ya'know, just so you don't have to go searching all over the internet for it.


