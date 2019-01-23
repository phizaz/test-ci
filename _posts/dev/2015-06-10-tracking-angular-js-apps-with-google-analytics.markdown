---
layout: post
title: Tracking Angular.js Apps with Google Analytics
date: 2015-06-10 09:48:00.000000000 +07:00
categories: dev
tags: web
---
Google Analytics is a great service. It provides us, web developers, variety of insights about our users. Obviously, the more we understand our clients the more we can push our websites to the better stage for them.

Usually, to put a Gogole Analytics service onto normal *"refreshy"* websites is not a hard work at all becasue Google has managed to create a very simple way to install it, just copy and paste the provided tracking code that's all.

However, that tracking code will be run only **once** per refresh. This works properly considering when we click on a hyperlink, the web page refreshes, then, of course, the tracking code is triggered and notices that you have got into a new page.

A problem arises when it comes to the world of *"refreshless"* or single-page websites (e.g. AngularJS) where the tracking code will be run only once at the load time. According to this, the tracking code will not be able to collect proper information about the user as before. Now, it is our duty to come up with solution to "live" with it.

<!--more-->

So far, the cleanest solution I found has been provided with an answer to this very problem posted on [Stackoverflow.com](http://stackoverflow.com/questions/10713708/tracking-google-analytics-page-views-with-angular-js)

The code snippet is as follows:  
(Note: I use *Angular UI router* instead of the native router)

```coffeescript
rootScope.$on '$stateChangeSuccess', () ->
	$window.ga 'send', 'pageview', { page: $location.url() }
```

Put this snippet to where you can make sure that this snippet will be run only **once right after** the page loads.

*Don't forget to inject $window and $location*

The above snippet will listen to every route change and call the Google Analytics service to acknowledge and log that action.

And also put your tracking code (get it from Google Analytics website) on the main page of your website let's say `index.html`

```coffeescript
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','//www.google-analytics.com/analytics.js','ga');

ga('create', '...', 'auto');
// we comment this because it has been included 
// with the code snippet above
// ga('send', 'pageview');
```

Now, everything is all set. You should get the same experience of using Google Analytics as the "refreshy" ones.
