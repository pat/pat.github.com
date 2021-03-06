---
title: "Using Thinking Sphinx with Cucumber"
redirect_from: "/posts/using_thinking_sphinx_with_cucumber/"
categories:
  - search
  - testing
  - cucumber
  - sphinx
  - thinking sphinx
  - ruby
  - rails
---
While I highly recommend you stub out your search requests in controller
unit tests/specs, I also recommend you give your full stack a work-out
when running search scenarios in Cucumber.

This has gotten a whole lot easier with the `ThinkingSphinx::Test` class
and the integrated Cucumber support, but it’s still not perfect, mainly
because generally everyone (correctly) keeps their database changes
within a transaction. Sphinx talks to your database outside Rails’
context, and so can’t see anything, unless you turn these transactions
off.

It’s not hard to turn transactions off in your `features/support/env.rb`
file:

    Cucumber::Rails::World.use_transactional_fixtures = false

But this makes Cucumber tests far more fragile, because either each
scenario can’t conflict with each other, or the database needs to be
cleaned before and after each scenario is run.

Pretty soon after I added the inital documentation for this, a few
expert Cucumber users pointed out that you can flag certain feature
files to be run without transactional fixtures, and the rest use the
default:

    @no-txn
    Feature: Searching
      In order to find things as easily as possible
      As a user
      I want to search across all data on the site

This is a good step in the right direction, but it’s not perfect -
you’ll still need to clean up the database. Writing steps to do that is
easy enough:

    Given /^a clean slate$/ do
      Object.subclasses_of(ActiveRecord::Base).each do |model|
        next unless model.table_exists?
        model.connection.execute "TRUNCATE TABLE `#{model.table_name}`"
      end
    end

(You can also use [Database
Cleaner](http://rubygems.org/gems/database_cleaner), as noted by Thilo
in the comments).

But adding that to the start and end of every single scenario isn’t
particularly DRY.

Thankfully, there’s `Before` and `After` hooks in Cucumber, and they can
be limited to scenarios marked with certain tags. Now we’re getting
somewhere!

    Before('@no-txn') do
      Given 'a clean slate'
    end

    After('@no-txn') do
      Given 'a clean slate'
    end

And here’s a bonus step, to make indexing data a little easier:

    Given /^the (\w+) indexes are processed$/ do |model|
      model = model.titleize.gsub(/\s/, '').constantize
      ThinkingSphinx::Test.index *model.sphinx_index_names
    end

So, how do things look now? Well, you can write your features normally -
just flag them with `no-txn`, and your database will be cleaned up both
before and after each scenario.

My current preferred approach is adding a file named
`features/support/sphinx.rb`, containing this code:

    require 'cucumber/thinking_sphinx/external_world'

    Cucumber::ThinkingSphinx::ExternalWorld.new

    Before('@no-txn') do
      Given 'a clean slate'
    end

    After('@no-txn') do
      Given 'a clean slate'
    end

And I put the step definitions in either
`features/step_definitions/common_steps.rb` or
`features/step_definitions/search_steps.rb`.

So, now you have no excuse to not use Thinking Sphinx with your Cucumber
suite. Get testing!

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
<a href="http://upstre.am">thilo</a> left a comment on 12 Mar,
2010:</div>

<div class="comment" markdown="1">
Hi Pat,  
nice write up.

The recent version of cucumber uses database\_cleaner. So that you just
need to add these steps.

Before(‘@no-txn’) do  
 DatabaseCleaner.start  
end

After(‘@no-txn’) do  
 DatabaseCleaner.clean  
end

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 13 Mar,
2010:</div>

<div class="comment" markdown="1">
Thanks Thilo - couldn’t find it mentioned in the cucumber source (0.4.4
or 0.6.3), but it’s definitely an option if people want to install that
gem. Have updated the blog post to mention it.

</div>
<div class="comment-author">
Neal Clark left a comment on 31 May, 2010:</div>

<div class="comment" markdown="1">
hey pat!

using a setup very similar to the one you described above, i get
intermittent failures on search. is this something you have experienced
as well?

i recall from your [testing sphinx
page](http://freelancing-god.github.com/ts/en/testing.html) that you
were sleeping a quarter of a second after re-indexing the models you
were testing. that is left that out of your Given /^the (\\w+) indexes
are processed$/ step definition. should i still need that?

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 1 Jun,
2010:</div>

<div class="comment" markdown="1">
Hi Neal

You will still need the sleep line - not sure why I left that out of
this blog post (I’ll amend it with a note at some point soon). If you’ve
got an older/slower machine, then you may need to increase the sleep
length, too.

When you say you get failures - do you mean the search results aren’t
there? (Sleeping so searchd can catch up should help that) Or do you
mean errors are being raised?

</div>
<div class="comment-author">
Neal Clark left a comment on 1 Jun, 2010:</div>

<div class="comment" markdown="1">
hey pat! thanks for getting back.

when i say that i get failures, i do mean that the search results aren’t
there. but it only happens sometimes, say, 1 out of 10 times i run the
test. i guess my machine is pretty fast … or something? i’ll sleep for
0.1 seconds and see if that works out.

thanks again!  
-n

</div>
</div>

