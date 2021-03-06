---
title: "Postcode API"
redirect_from: "/posts/postcode_api/"
categories:
  - REST
  - api
  - postcodes
  - merb
  - ruby
  - australia
---
A couple of weeks ago I quickly coded a basic webservice - using
[Merb](http://merbivore.com) - for Australian Postcode data. Just got
hosting for it sorted out last night, so now I can blog about it.

### [Postie](http://auspostie.com)

The emphasis is on simple - you can search by postcode or suburb, and
get the data back either as a HTML page, XML, or JSON. That’s pretty
much it. The request urls aren’t complicated, either:

-   [auspostie.com/Brunswick](http://auspostie.com/Brunswick)
-   [auspostie.com/Brunswick.js](http://auspostie.com/Brunswick.js)
-   [auspostie.com/Brunswick.xml](http://auspostie.com/Brunswick.xml)
-   [auspostie.com/2782](http://auspostie.com/2782)
-   [auspostie.com/2782.js](http://auspostie.com/2782.js)
-   [auspostie.com/2782.xml](http://auspostie.com/2782.xml)

Suburb requests use partial matching, so you don’t need the full suburb
name. If you want the JSON returned with the MIME type of
`application/json`, use the `.json` extension.

I’ve no plans at this point on using this, but perhaps it’s useful for
someone out there - if so, would love to hear about it.

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
<a href="http://toolmantim.com">Tim Lucas</a> left a comment on 30 Dec,
2007:</div>

<div class="comment" markdown="1">
That’s really cool. I’m sure someone will find it handy.

</div>
<div class="comment-author">
<a href="http://nomad-labs.com">shoaib</a> left a comment on 30 Dec,
2007:</div>

<div class="comment" markdown="1">
well done Pat, this will come in handy

</div>
<div class="comment-author">
<a href="http://drnicwilliams.com">Dr Nic</a> left a comment on 30 Dec,
2007:</div>

<div class="comment" markdown="1">
Ooh tastie indeed.

A cmd-line wrapper for the API would be like a tastie toasted sandwich.

</div>
<div class="comment-author">
<a href="http://www.marstononline.com">Marston A.</a> left a comment on
19 Jan, 2008:</div>

<div class="comment" markdown="1">
Hey Pat,

This looks like a great tool indeed! Would you happen to know of any
similar API’s/Plug-in’s for U.S. Postal Codes?

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 20 Jan,
2008:</div>

<div class="comment" markdown="1">
Heya Marston

It looks like the USPS has a couple of APIs that do the job - there’s
“documentation on their site”http://www.usps.com/webtools/technical.htm

Not quite as simple as mine… but if you find a csv or similar of *all*
the US zip codes and matching cities and states, I could look at
building an equivalent service for it.

</div>
<div class="comment-author">
<a href="http://perthonline.net">Matthew Lock</a> left a comment on 20
May, 2011:</div>

<div class="comment" markdown="1">
Hello, could you make your webservice available via JSONP by adding a
?callback= parameter to your .js url? That way we can call your API from
javascript in a web page.

http://en.wikipedia.org/wiki/JSONP

http://api.jquery.com/jQuery.getJSON/

</div>
</div>

