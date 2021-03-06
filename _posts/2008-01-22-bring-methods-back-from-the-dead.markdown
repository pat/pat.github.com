---
title: "Bring Methods Back From The Dead"
redirect_from: "/posts/bring_methods_back_from_the_dead/"
categories:
  - ruby
  - rails
---
Today was the first time I’d come across Ruby’s `undef_method` - it’s
used in a few places in Rails, particularly with ActiveRecord’s
associations. While I see the point of it, there were a few methods I
wanted back - and I’ve figured out how to do it - you need to grab the
method definition from the superclass. Here’s an example:

    class AntiString
      undef_method to_s
    end

    AntiString.new.to_s
      #=> NameError: undefined method `to_s' for class `AntiString'

    AntiString.send(:define_method, :to_s,
      AntiString.superclass.instance_method(:to_s))

    AntiString.new.to_s #=> "#"

Now, the obvious caveat - if the method was originally defined in that
class, not the superclass, then I think you’re out of luck. Although I’m
guessing you’ll rarely be in a position where you need to resurrect a
method like this anyway.
