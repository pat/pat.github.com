---
title: "Sphinx: A Primer"
redirect_from: "/posts/sphinx_a_primer/"
categories:
  - search
  - sphinx
  - ruby
  - rails
---
On Thursday night I presented to the [Melbourne Ruby
Group](http://rubyonrails.com.au/melbourne-meetups) about
[Sphinx](http://sphinxsearch.com/) - first with a non-Ruby perspective,
and then using Ruby, and more specifically Rails. I’ll be presenting
again at the Sydney group in a couple of weeks, but I am also adapting
the talk to a few blog posts - to allow a bit more detail in a few
doses.

First up: Sphinx itself. Why should you read this? Because understanding
Sphinx will help you use whichever library (Ruby or otherwise) smarter.
It might also teach you some things you had no idea about (ie: this is
the article I should have read when I started using Sphinx).

### What is Sphinx?

Sphinx is a search engine. You feed it documents, each with a unique
identifier and a bunch of text, and then you can send it search terms,
and it will tell you the most relevant documents that match them. If
you’re familiar with Lucene, Ferret or Solr, it’s pretty similar to
those systems. You get the daemon running, your data indexed, and then
using a client of some sort, start searching.

When indexing your data, Sphinx talks directly to your data source
itself - which must be one of MySQL, PostgreSQL, or XML files - which
means it can be very fast to index (if your SQL statements aren’t too
complex, anyway).

### Sphinx Structure

A Sphinx daemon (the process known as **searchd**) can talk to a
collection of **indexes**, and each index can have a collection of
**sources**. Sphinx can be directed to search a specific index, or all
of them, but you can’t limit the search to a specific source explicitly.

Each source tracks a set of documents, and each document is made up of
**fields** and **attributes**. While in other areas of software you
could use those two terms interchangeably, they have distinct meanings
in Sphinx (and thus require their own sections in this post).

### Fields

Fields are the content for your search queries - so if you want words
tied to a specific document, you better make sure they’re in a field in
your source. They are only string data - you could have numbers and
dates and such in your fields, but Sphinx will only treat them as
strings, nothing else.

### Attributes

Attributes are used for **sorting**, **filtering** and **grouping** your
search results. Their values do not get paid any attention by Sphinx for
search terms, though, and they’re limited to the following data types:
integers, floats, datetimes (as Unix timestamps - and thus integers
anyway), booleans, and strings. Take note that string attributes are
converted to **ordinal** integers, which is especially useful for
sorting, but not much else.

### Multi-Value Attributes

There is also support in Sphinx to handle arrays of attributes for a
single document - which go by the name of **multi-value attributes**.
Currently (Sphinx version 0.9.8rc2) only integers are supported, so this
isn’t quite as flexible as normal attributes, but it’s worth keeping in
mind.

### Filters

Filters are useful with attributes to limit your searches to certain
sets of results - for example, limiting a forum post search to entries
by a specific user id. Sphinx’s filters accept arrays or ranges - so if
filtering by a single value, just put that in an array. The range
filters are particularly useful for getting results from a certain time
span.

### Relevance

Relevancy is the default sorting order for Sphinx. I’ve no idea exactly
how it is calculated, but there are a couple of things you can do easily
enough in your queries to influence it. The first is **index-level
weighting**, where you give specific indexes higher rankings than
others. The other, similar in nature, but at a lower level, is **field
weightings**. Generally these are set before each query, but it will
depend on the library you use.

### Keeping Your Indexes Updated

One thing that sets Sphinx apart from Ferret and other search engines is
that there is no way to update fields for a specific document in your
indexes. The main approach around this is having **delta indexes** - a
small index with all the recent changes (which will be super-fast to
index), so Sphinx will include that and the main index for its searches.
Of the Rails plugins, both [Thinking
Sphinx](http://ts.freelancing-gods.com) and
[Ultrasphinx](http://blog.evanweaver.com/files/doc/fauna/ultrasphinx/)
have support for this - I’ve no idea for other languages, mind you.

### What’s next?

Next is when we’ll dive into some actual code - we’ll go through some of
the common tasks for setting up Sphinx with Rails using Thinking Sphinx.

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
Thuva left a comment on 26 Apr, 2008:</div>

<div class="comment" markdown="1">
Thank you for the great overview of Sphinx. I’m looking forward to your
next post in this series.

By the way, I’m using Thinking Sphinx with great success in a project
that is under development.

</div>
<div class="comment-author">
<a href="http://toolmantim.com">Tim Lucas</a> left a comment on 27 Apr,
2008:</div>

<div class="comment" markdown="1">
Would love to see such a succinct overview of the basics make it back
the official site. The official manual is considerably less accessible.

Look forward to the preso!

</div>
<div class="comment-author">
Brian Ablaza left a comment on 5 May, 2008:</div>

<div class="comment" markdown="1">
What can I do to try and resolve a problem or ask a question?

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 5 May,
2008:</div>

<div class="comment" markdown="1">
Brian: Either post a comment here or email me (pat at this domain) -
more than happy to help.

</div>
<div class="comment-author">
Brian Ablaza left a comment on 6 May, 2008:</div>

<div class="comment" markdown="1">
I’ll send you an email. Thanks.

</div>
<div class="comment-author">
<a href="http://www.embian.com">Jae-Jun Hwang</a> left a comment on 6
May, 2008:</div>

<div class="comment" markdown="1">
Thank you for writing ThinkingSphinx and Riddle. It’s been wonderful
product for my development.

BTW, I experimented TS, and made some patch to 1) handle CJK
charset\_tables, 2) prefix\_fields, 3) delta+dirty etc. I’ll send it to
you email,  
so could you please take a look?

</div>
<div class="comment-author">
Brian Ablaza left a comment on 7 May, 2008:</div>

<div class="comment" markdown="1">
Pat: That fixed it. The .gz file was missing a few components, but I
pulled them off of github and now it works beautifully.

Thanks for your help, and thanks for this plugin.

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 7 May,
2008:</div>

<div class="comment" markdown="1">
Brian: great to know it’s all working out for you now :)

</div>
<div class="comment-author">
Brian Ablaza left a comment on 8 May, 2008:</div>

<div class="comment" markdown="1">
Pat:

In delta.rb you say:

\# This module contains all the delta-related code for models. There
isn’t  
 \# really anything you need to call manually in here - except perhaps  
 \# index\_delta, but not sure what reason why.

Here’s a reason. My DB is a legacy database, so I can’t change its
structure. I’d like to trigger a delta re-index based on the max
modifydate (an integer) in the File table.

So how do I call index\_delta?

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 9 May,
2008:</div>

<div class="comment" markdown="1">
Brian: Not really sure what the best approach for you is - I can’t think
of anything simple. I’d suggest you look at reimplementing delta.rb to
use the modified date as a reference - but you’re going to need to
previous main index date stored somewhere in the database, as a
reference point…

You’ll almost certainly need to change things in configuration.rb as
well.

The easiest solution is just to skip delta indexes and run normal
indexing often.

</div>
<div class="comment-author">
Chris Heald left a comment on 9 May, 2008:</div>

<div class="comment" markdown="1">
Thinking Sphinx looks fantastic. I’m looking forward to giving it a go!

However, in setting it up in my app, I discovered that it chokes on
namespaced models in subdirectories (load\_models in configuration.rb,
specifically) - I’ve fixed it locally, but I’d like to submit a patch,
if there’s an appropriate place to do so. I didn’t see any kind of issue
tracker or whatnot linked to the project.

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 9 May,
2008:</div>

<div class="comment" markdown="1">
Chris: Would love the patch! Either send me the file directly (via
email) - or fork a copy on GitHub, commit your change to that, and I’ll
pull it straight in from there (and you’ll get the credit for the
commit)

</div>
<div class="comment-author">
Chris Heald left a comment on 9 May, 2008:</div>

<div class="comment" markdown="1">
Sure! I’ve just cloned it on github - you’ll find my commit at
http://github.com/cheald/thinking-sphinx/commit/de852124fb55df07b607aee0804d71317573fbc4

I actually have two commits in there, with three fixes. I’d suggest
checking ’em before committing, as you know your code better than I, and
I don’t know if I mistakenly screwed something up in the process. Seems
to work fine against my app, though!

</div>
<div class="comment-author">
Pratik left a comment on 27 Jul, 2009:</div>

<div class="comment" markdown="1">
Thanks for a very well written article!  
Do you know how we can restrict the fields from which we search. I mean,
i have two indexed columns, “bio” and “status”. When the user is looking
for the string “active”, it should only search from the status column
and not from bio.

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 27 Jul,
2009:</div>

<div class="comment" markdown="1">
Pratik: depends how you want to go about this… is the user entering any
query text they like (which may or may not include the word ‘active’)?
Or are you specifically directing specific input to specific fields?

If the latter, use “@status active” for your query.

Or, `:conditions => {:status => 'active'}` if you’re using Thinking
Sphinx.

The former, there’s no way to indicate a particular word should only be
cared about if it exists in certain fields. You could set field\_weights
to make the status column more important though:

    # with Riddle
    client.field_weights = {'status' => 10}
    # with Thinking Sphinx
    Model.search :field_weights => {'status' => 10}
    # Or in define_index block
    set_property :field_weights => {'status' => 10}

Any fields not mentioned in the weightings keep their default weight of
1.

</div>
</div>

