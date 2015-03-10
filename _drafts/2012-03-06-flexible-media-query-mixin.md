---
layout: post
title:  "Making our Media Query Mixins More Flexible"
date:   2014-03-07 
author: David Conner
categories: sass tips
permalink: /flexible-media-query-mixins/
desc: "Our content should determine what the breakpoints are. Any mixin that we use should be flexible enough to add them when and where they need be"
image: flexible.jpg
---

Media Queries are how we all make our sites responsive. Some simple sites may only require a few but on large projects we find ourselves writing them dozens of times in order to wrangle in the content. Though relatively small, writing a query over and over can get tedious which is why most people choose to use a mixin to try to minimize the typing. There are a ton of them out there ranging from simple to complex. Many of them opted to name the breakpoints with the idea that the names would be more intuitive than a numeric `em` value. The mixin would look something like:

{% highlight scss %}
@mixin breakpoint($point) {
	@if $point == small {
		@media (min-width: 24em) { @content; }
	}
	@else if $point == medium {
		@media (min-width: 46.8em) { @content; }
	}
	@else if $point == large {
		@media (min-width: 50em)  { @content; }
	}
}
{% endhighlight %}

You would then call the mixin and pass it the name of the breakpoint.

{% highlight scss %}
.element {
	@include breakpoint(medium) {
		...
	}
}
{% endhighlight %}

Though I used this approach myself I have found it to be flawed for a few reasons. 

##What the Hell does 35em Look Like

There are some pretty good arguments (<a href="http://bradfrost.com/blog/post/7-habits-of-highly-effective-media-queries/#relative">here</a> and <a href="http://blog.cloudfour.com/the-ems-have-it-proportional-media-queries-ftw/">here</a>) on why we should be using ems over pixels in our media queries but the problem is we are still seeing size in pixels

Whether its a browser plugin or the element inspector or the new resize bubble on Codepen.io we are constantly given window width information in pixels units. It is no wonder then that when we think of window size we think pixels as well. So when writing media queries we must do a conversion in order to use ems. Whether you  do the calculations in your head or you use a calculator it is still one more action to complete. This adds up over the course of a project and will slow you down. 

So we create mixins, like the one above, that provides named breakpoints and we have a vague recollection of what size (in pixels) they correlate to. This sort of takes away our pixel conversion but now what happens when you need a breakpoint that is not defined? Or, what if you want to have max-width or height?

##Content Should Determine Breakpoints  
Our content should determine what the breakpoints are. We start with the small screen first, and then expand it till the content no longer looks good. We then add a breakpoint. Any mixin that we use should be flexible enough to add them when and where they need be and having arbitrary preset sizes is not the  best way to go. 

##One Solution
I have gotten away from naming my breakpoints and from creating them at "small", "medium" and "large" sizes. The mixin below is a versatile solution that allows for the creation of breakpoints as the content dictates. It defaults to `min-width:` for a terse include but arguments can be passed for min, max, height or width. This added flexibility means we can use this mixin for most of our query needs. 

It also embraces our pixel mindset. Since we are getting fed pixels by our tools this mixin is built to take it right back. Let the computer to do the math. The mixin takes the pixel input and divides it by the base font size, giving us our `em` units.  

{% highlight scss %}
//base font
$bf: 16;

@mixin bp($bp, $min-max: min-width) {
	$em: $bp/$bf;
	@media(#{$min-max}: #{$em}em) { @content; }
}

//including the mixin
.element {
	@include bp(600) {
		...
	}
}
{% endhighlight %}


##Final Thoughts
This mixin is terse, versatile, takes care of the `px` to `em` conversion and gives a bump to my productivity. You can still add predefined, named breakpoints with a variable if needed so it is still compatible with that school of thought.  

We all have our own style of code, so much of this is just preference. The true test is which mixin helps you make things faster. If you have a media query mixin that helps you please share in the comments. 

