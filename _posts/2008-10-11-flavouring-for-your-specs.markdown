---
title: "Flavouring For Your Specs"
redirect_from: "/posts/flavouring_for_your_specs/"
categories:
  - rspec
  - versions
  - garlic
  - ginger
  - gems
  - ruby
  - rails
---
One of the things I’ve tried to do with [Thinking
Sphinx](http://ts.freelancing-gods.com) has been to keep it friendly for
a few different versions of Rails/ActiveRecord: 1.2.6, 2.0.x and 2.1.x.
It’s not that easy, because I know I don’t think about which methods are
pre-Rails-2.0, and which are even newer. And add on top of that the
large (and very welcome) number of
[contributors](http://github.com/freelancing-god/thinking-sphinx/network/members),
and it becomes a bit of a headache.

I was introduced to a potential aid for this at RailsConf EU, by fellow
Australian [Ian White](http://blog.ardes.com/ian), in the form of his
library [Garlic](http://github.com/ianwhite/garlic/tree/master). It
takes Rails plugins, instantiates them within Rails applications for
each defined environment, and then runs their specs.

That in itself is pretty damn cool - except I don’t run Thinking
Sphinx’s specs from within Rails - and it’s really only reliant on
ActiveRecord (so it can be used in Merb). So, I’ve spent an afternoon
coding up something slightly different, but in something of an
acknowledgement to Ian’s library, my solution is called
[Ginger](http://github.com/freelancing-god/ginger/tree).

Now, it’s not exhaustively tested, but I’ve got it to the point where it
runs as I would expect it to with Thinking Sphinx, so consider it ready
for release.

If you’re interested in using it, here’s the basic guidelines. First up,
install the gem:

    sudo gem install freelancing-god-ginger --source=http://gems.github.com

(GitHub doesn’t have the gem loaded at the moment though, so you’ll have
to install it manually)

    git clone git://github.com/freelancing-god/ginger.git
    cd ginger
    gem build ginger.gemspec
    sudo gem install --local ginger-1.0.0.gem

Then, in your `spec_helper.rb` file (or equivalent - this should work
for Test::Unit as well as RSpec), add:

    require 'ginger'

You’ll want to make sure that `require` is before any other `require`
calls to libraries you want to test multiple versions of.

Next, you need to create a scenario file, outlining the different
testing situations Ginger should run through. These details go in a file
called `ginger_scenarios.rb` in the root of your project. Here’s my
current one for Thinking Sphinx:

    require 'ginger'

    Ginger.configure do |config|
      config.aliases["active_record"] = "activerecord"

      ar_1_2_6 = Ginger::Scenario.new
      ar_1_2_6[/^active_?record$/] = "1.15.6"

      ar_2_0_2 = Ginger::Scenario.new
      ar_2_0_2[/^active_?record$/] = "2.0.2"

      ar_2_1_1 = Ginger::Scenario.new
      ar_2_1_1[/^active_?record$/] = "2.1.1"

      config.scenarios << ar_1_2_6 << ar_2_0_2 << ar_2_1_1
    end

I’m adding three different scenarios - one for each version of
ActiveRecord I want to test. I’m also adding an alias, as sometimes
people have an underscore in the `require` calls, and while the
`require` method isn’t fussy, the `gem` method is. That’s also the
reason for the regular expressions, but strings will work as well.

If you want, you can have multiple gem requirements for each scenario -
`Ginger::Scenario` is subclassed from `Hash`, so just add more key/value
pairs as needed.

    sphinx_scenario = Ginger::Scenario.new
    sphinx_scenario["riddle"] = "0.9.8" 
    sphinx_scenario["thinking_sphinx"] = "0.9.8"

And don’t forget to add each scenario to `config`’s collection.

The last step is the most important - running your tests through each
scenario! This is done with the `ginger` command line tool - any
arguments given to it get echoed onto `rake`, so in theory it should
work with whatever rake task you like. I’ve only tested it with RSpec
though.

    ginger spec
    ginger test
    ginger spec:unit

This was built to scratch my itch, obviously, but happy to accept
patches or suggestions. GitHub, as always, is the best way to do this -
fork to your heart’s content.

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
<a href="http://drnicwilliams.com">Dr Nic</a> left a comment on 13 Oct,
2008:</div>

<div class="comment" markdown="1">
This looks nifty. I hacked up a “test against diff AR versions + edge
rails” system for Composite Keys gem, but it used rake tasks + setting
up ENV variables.

In CPK, each tester might test against all/some AR versions, plus a
subset of database adapters. So if we used ginger, the config would
still be per-tester, so I guess we’d have a config/ginger.rb.sample and
a default config/ginger.rb or something.

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 13 Oct,
2008:</div>

<div class="comment" markdown="1">
Dr Nic: yup, sounds like a good way to approach it. Although the
`ginger_scenario.rb` file needs to exist in the project’s base
directory, not within `config`.

</div>
<div class="comment-author">
ed left a comment on 19 Oct, 2008:</div>

<div class="comment" markdown="1">
Great post, very well written code.

Thanks

</div>
</div>

