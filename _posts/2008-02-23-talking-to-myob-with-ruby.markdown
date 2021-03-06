---
title: "Talking to MYOB with Ruby"
redirect_from: "/posts/talking_to_myob_with_ruby/"
categories:
  - merb
  - myob
  - odbc
  - ruby
  - rails
---
Okay, time for another code post. Prompted by a comment on an [earlier
post about Merb and
MYOB](http://freelancing-gods.com/posts/mixing_merb_and_myob), I thought
I’d provide some more detail on how to talk to MYOB using Ruby, for any
other people stuck in a similar position.

### ODBC Bindings

If you like crafting your own SQL, this is the easiest approach.
Firstly, you’ll need to download Christian Werner’s [ODBC Bindings for
Ruby](http://ch-werner.de/rubyodbc/), and install it using the following
commands (or something along these lines) from within the source
directory:

    ruby extconf.rb
    make
    sudo make install

[Documentation](http://ch-werner.de/rubyodbc/odbc.html) is a bit light
on examples - and it’s really just a hook into the C libraries for ODBC,
so good luck reading the source. Here’s some of the basic things you’ll
need to do. (Don’t forget to `require 'odbc'` first, of course).

#### Setting up a Connection

You can either go through the DSN collection, or connect using the
string name of the DSN (which in this case we’ll assume is ‘MYOB’):

    dsn = ODBC.datasources.detect { |source| source.name == "MYOB" }
    database = ODBC::Database.connect dsn

    # or

    database = ODBC::Database.connect "MYOB"

Note that the `ODBC.datasources` collection only contains system DSNs,
not User-level ones. Not sure why, or how to access the latter.

#### SELECT Statements

    statement = database.prepare "SELECT * FROM Customers"
    statement.execute
    statement.each_hash d |row|
      # results accessed as row["column_name"]
    end
    statement.drop

Make sure you drop the statement once you’re done with it, otherwise
you’ll get complaints when you stop ruby that the statements weren’t all
closed and dropped.

There’s other ways to access results - you can go through results as you
see fit using `fetch` or `fetch_hash`, depending on whether you want an
array or hash of the record.

#### INSERT Statements

With other SQL statements (although there’s no use of UPDATE or DEETE in
MYOB connections), you don’t really care about the results, so that
makes things a bit simpler, you can just call
`database.do("INSERT INTO Import_Customer_Cards (...) VALUES (...)")`.
The `do` method creates the statement, executes it and then drops it for
you. MYOB makes this a little more complex though, as it all must be
done in transactions - not that that’s a bad thing.

    database.transaction do |env|
      env.do("INSERT INTO Import_Customer_Cards (...) VALUES (...)")
    end

This won’t actually do anything though - and it won’t throw up an error
or warning. The problem is you need to turn `autocommit` mode off,
because the MYOB ODBC drivers don’t like it (it’s not a problem with
Christian’s bindings). So, back when you create your connection:

    database = ODBC::Database.connect "MYOB"
    database.autocommit = false

### ActiveRecord

If you prefer some level of abstraction above the messy SQL, then it
might be worth looking at OpenLink’s [ActiveRecord
ODBCAdapter](http://odbc-rails.rubyforge.org/). I recommend the gem
instead of the plugin, as the plugin takes it upon itself to modify your
code, which I don’t like.

    sudo gem install odbc-rails

Now, I’m not an ODBC expert, but there seems to be different flavours of
ODBC connections possible - and MYOB’s is not one supported by this gem.
So, if you’re using Rails, put the gem into vendor (and for Merb, into
the local gems folder), then modify it with [this
patch](http://freelancing-gods.com/system/odbc-rails-patch-for-myob.diff).
I make no promises for it being stable or reliable - but I’ve not had
any problems yet.

Of course, using ActiveRecord is viable if you’re just reading data out
- but if you want to write as well as read, then there’s issues. MYOB
has separate write tables (prefixed with ‘Import\_’), and they have
denormalised schemas compared to the read-equivalents.

For database.yml, you’ll need something like the following:

    development:
      adapter: odbc
      dsn: MYOB

### Everything Else

In both the situations above, I’ve not put usernames or passwords into
the connections - you can, but that is already handled by the ODBC DSN,
so I keep my code that bit simpler.

For those who are new to coding for MYOB - it does cost money to get a
developer account (several hundred dollars per year), which is the only
way to get write access. I think read access is a once off fee of a
couple of hundred dollars, but that’s specific to a MYOB file.

And if anyone is wondering - while I was initially using the odbc-rails
library, I’ve now switched to constructing the SQL myself and just using
the bindings, because of the table issues.

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
<a href="http://rhnh.net">Xavier Shay</a> left a comment on 23 Feb,
2008:</div>

<div class="comment" markdown="1">
Sweet, I can see this coming in handy

</div>
<div class="comment-author">
<a href="http://luminousmonkey.org/">Mike Aldred</a> left a comment on
21 Apr, 2008:</div>

<div class="comment" markdown="1">
I’m currently interested in getting a work Rails app integrated with
MYOB for our accounts, and I’ve got my developer licence (just waiting
on things to arrive).

I do have a question though, you using an ODBC-ODBC bridge or something?
Doesn’t the MYOB ODBC driver only run under Windows?

Cheers

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 21 Apr,
2008:</div>

<div class="comment" markdown="1">
Hi Mike

Yes, MYOB’s driver does only work on Windows. There are bridges around -
but none that were free (or even trial versions) that work on 10.5, my
development platform of choice.

The project I did a lot of MYOB/Ruby work for involved a Windows server
with the ODBC Driver and some generic ruby code that parsed XML provided
from a Rails app on a \*nix machine. Testing the code was not
particularly fun, involving Parallels and Cygwin.

</div>
<div class="comment-author">
<a href="http://luminousmonkey.org/">Mike Aldred</a> left a comment on
23 Apr, 2008:</div>

<div class="comment" markdown="1">
Ahh, cool.

I was wondering about all the sudo commands.  
I’ve got a Solaris server that’s going to host the database and web
front end, and I’ll probably write up a little ruby app for the Windows
end.

I’ll do a post of my efforts here sometime, just to help anyone else
thinking of the same thing.

Cheers

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 23 Apr,
2008:</div>

<div class="comment" markdown="1">
Mike, would be great to see a write-up on how you go about it - always
good to see some different approaches.

</div>
<div class="comment-author">
<a href="http://luminousmonkey.org/">Mike Aldred</a> left a comment on
24 Apr, 2008:</div>

<div class="comment" markdown="1">
Sorry to bother you again Pat.

The link to the diff seems to be broken. I’m assuming you’re just adding
openrda to the dbmsNameToSym function in odbc\_adapter.rb?

Cheers

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 24 Apr,
2008:</div>

<div class="comment" markdown="1">
Hi Mike - looks like I never tested that link. The file wasn’t named
properly *and* I mispelt my own domain. Sorry about that.

And yeah, I wasn’t doing much more than adding openrda as a recognised
dbms type.

</div>
<div class="comment-author">
Faber left a comment on 13 Nov, 2008:</div>

<div class="comment" markdown="1">
Sori for my newbie question :D

After buying MYOB’s license and installing it, can we customize it
freely? Without having to buy another license or the like?

Thanks

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 13 Nov,
2008:</div>

<div class="comment" markdown="1">
Hi Faber

I’m afraid I’m not a MYOB expert, so I can’t really help you with that.
Best to ask MYOB support (or on their forums).

</div>
</div>

