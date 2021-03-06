---
title: "Rewriting Thinking Sphinx: Loading Only When Necessary"
redirect_from: "/posts/rewriting_thinking_sphinx_loading_only_when_necessary/"
categories:
  - sphinx
  - thinking sphinx
  - ruby
  - rails
---
I’ve obviously been neglecting this blog - even a complete rewrite of
Thinking Sphinx hasn’t garnered a mention yet! Time to remedy this…

There’s plenty to focus on with Thinking Sphinx v3 (released just under
six months ago), because a ton has changed - but that’s pretty well
covered [in the
documentation](http://pat.github.io/thinking-sphinx/upgrading.html). I’m
going to cover one thing per post instead.

First up: index definitions are now located in their own files, located
in the `app/indices` directory. Given they can get quite complex, I
think they’re warranted to have their own files - and besides, let’s
keep our concerns separate, instead of stuffing everything into the
models (yes, I’m a firm proponent of skinny everything, not just skinny
controllers).

So, instead of this within your model:

    class User < ActiveRecord::Base
      # ...

      define_index do
        indexes first_name, last_name, country
      end

      # ...
    end

You now create a file called `user_index.rb` (or whatever, really, as
long it ends with `.rb`) and place it in `app/indices`:

    ThinkingSphinx::Index.define :user, :with => :active_record do
      indexes first_name, last_name, country
    end

You’ll note the model is now specified with a symbol, and we’re
providing an index type via the `:with` option. At the moment, the
latter is always `:active_record` unless you’re using Sphinx’s real-time
indices (which are definitely beta-status in Thinking Sphinx). The model
name as a symbol, however, represents one of the biggest gains from this
shift.

In previous versions of Thinking Sphinx, to discover all of the index
definitions that existed within your app, the gem would load *all* of
your models. Initial versions did this every time your app initialised,
though that later changed so they the models and index definitions were
loaded only when necessary.

Except, it *was* necessary if a search was being run, or even just if a
model was modified (because updates to Sphinx’s index files could be
required) - which is the majority of Rails requests, really. And yes,
this information was cached between requests like the rest of Rails,
except - like the rest of Rails - in your development environment.

Loading all your models is quite a speed hit - so this could be pretty
painful for applications with a large number of models.

There were further workarounds added (such as the `indexed_models`
option in `config/sphinx.yml`), but it became clear that this approach
was far from ideal. And of course, there’s separation of concerns and
skinny models and so on.

This gives some hint as to why we don’t provide the model class itself
when defining indexes - because we don’t want to load our models until
we absolutely have to, but we *do* get a reference to them. The index
definition logic is provided in a block, which means it’ll only be
evaluated when necessary as well.

This doesn’t get around the issue of knowing when changes to model
instances occur though, so this got dealt with in two ways. Firstly:
delta index settings are now an argument at the top of the index, not
within the logic block:

    ThinkingSphinx::Index.define(
      :user, :with => :active_record, :delta => true
    ) do
      # ...
    end

And attribute updating is no longer part of the default set of features.

This means Thinking Sphinx can now know whether deltas are involved
*before* evaluating the index definition logic block - and thus, the
callbacks are lighter and smarter.

The end result is thus:

-   Thinking Sphinx only loads the index definitions when they’re
    needed;
-   They, in turn, only load the models and definition logic when
    required;
-   Each index now gets its own file;
-   Your models stay cleaner; and
-   Request times are faster.

Overall, a vast improvement.
