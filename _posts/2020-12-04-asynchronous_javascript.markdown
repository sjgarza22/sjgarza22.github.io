---
layout: post
title:      "Asynchronous JavaScript"
date:       2020-12-04 05:40:31 +0000
permalink:  asynchronous_javascript
---

If you're on Mod 4, this is probably a concept you're just learning about, and if you're like me, you may be struggling to understand this concept just a bit. If you are, this blog post is for you. By the end of this blog post, you should understand the difference between Asynchronous and Synchronous, and how Asynchronous code works in JavaScript.
#### What is the difference between Asynchronous v. Synchronous?
The difference is that Asynchronous allows us to run multiple tasks at once, while Synchronous means we run tasks one at a time in the order they appear. Defining Async v. Sync was the easy part, and it may be that you understand the difference already. It's when it applies to our JavaScript code that maybe things are getting a bit muddled for you, as they were for me. Which brings us to our next question...
#### How does Asynchronous JavaScript work?
Well, I believe the best example of Async JS is with a fetch request. Let's take a look at the following function:

```
function loadApiData() => {

let loader = `<div id="boxLoading"></div>`;
document.getElementById('results').innerHTML = loader;

fetch(mainUrl, {
        method: "GET",
        headers: {"Content-Type": "application/json"}
    })
    .then(response => response.json())
    .then(json => {
		    document.getElementByID('boxLoading').remove();
        json.forEach(item => {
            result +=
                `<div>
                    <h5> ID: ${item.id} </h5>
                    <ul>
                        <li>Name: ${item.name}</li>
                        <li>Year: ${item.year}</li>
                        <li>Note: ${item.note}</li>
                    </ul>
                </div>`;
            document.getElementById('results').innerHTML = result;
        })
    }
};
```

You're probably familar with the latter half of the code here, but you may be wondering what those first two lines do. It's not obvious because we don't have the CSS code here, but this will call forth a loading animation onto our web page. Every project is different, but in my Mod 4 project, it actually takes a few seconds before the results populate onto the screen. Without that animation, all the user would do is stare at a blank screen, and they may think my site is broken. We don't want that.

This is where Async JS comes into play. Glancing at the code, you might think that this would run syncronously, but thanks to the JavaScript  devolopers the fetch function was created to be run asyncronously. Unfortunatley, if you're wondering how the fetch method works under the hood, this isn't what this article is about. We're here to understand how Async JS works in theory, or essentially, what happens when we run code that can be used asyncronously.

Remember, that when we load JavaScript onto the browser, it does two passes. The first goes over the document and loads the code. It's in the second pass that we actually run the code. So, when our brower does that first pass over our code, it's going to go, "Oh! Here's a fetch request, well, that's an asyncronous function and I'm going to treat it as such." Now when we call our loadApiData function, it will run any code inside that function while running that fetch request AT. THE. SAME. TIME!

![Man Screaming and Foaming at the Mouth](https://external-content.duckduckgo.com/iu/?u=https%3A%2F%2F66.media.tumblr.com%2Fb9dc04b2ccf7a737e3b6b0b9b2dad8e4%2Ftumblr_inline_p8s7e2miHA1rjbs0f_540.gif&f=1&nofb=1)

Yeah, I know. Cool, huh?

Hopefully this article helped you understand how Async JavaScript works and you can use this knowledge to write better code and ace your Mod 4 review.
