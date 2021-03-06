---
title: "Thinking Sphinx PDF at Peepcode"
redirect_from: "/posts/thinking_sphinx_pdf_at_peepcode/"
categories:
  - pdf
  - peepcode
  - sphinx
  - ruby
  - rails
---
A quick note to let anyone using (or interested in using)
[Sphinx](http://sphinxsearch.com/) and/or [Thinking
Sphinx](http://ts.freelancing-gods.com/) that [my Peepcode
PDF](http://peepcode.com/products/thinking-sphinx-pdf) has just been
published, and contains a wealth of information on how to mix Sphinx
with ActiveRecord (via Rails or Merb).

It’s been great working with [Geoffrey Grosenbach](http://topfunky.com/)
to get this written up, and I’m pretty stoked to see the final results -
hopefully others will enjoy it as well.

Also, a massive thank you to all the contributors to Thinking Sphinx -
it wouldn’t be quite so cool if it wasn’t for all the patches
(facilitated by [GitHub’s
forking](http://github.com/freelancing-god/thinking-sphinx/network/members)).

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
<a href="http://semanticart.com">Jeff</a> left a comment on 28 Oct,
2008:</div>

<div class="comment" markdown="1">
I’m excited about thinking sphinx, but I’ve never been able to get past
an issue where a table whose id is a varchar refuses to be indexed… it
throws “WARNING: duplicate document ids found” and refuses to index
properly. I tried coding around the sql\_query \* 1 + 0 AS \`id\` bit on
a hunch, but that doesn’t seem to solve it.

Any advice? I’m otherwise ready to shell out the cash for this pdf.

</div>
<div class="comment-author">
<a href="http://semanticart.com">jeff</a> left a comment on 28 Oct,
2008:</div>

<div class="comment" markdown="1">
I should note that the varchar ids are unique in the database.

</div>
<div class="comment-author">
<a href="http://semanticart.com">Jeff</a> left a comment on 28 Oct,
2008:</div>

<div class="comment" markdown="1">
So it appears that this is a limitation of sphinx itself. Sorry for the
troubles. Even sorrier that I can’t use sphinx on this project.

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 29 Oct,
2008:</div>

<div class="comment" markdown="1">
Hi Jeff

Yup, unfortunately Sphinx requires integer IDs. Sorry it’s not a
solution that’ll work for you.

</div>
<div class="comment-author">
tashfeen left a comment on 20 Dec, 2008:</div>

<div class="comment" markdown="1">
i get the following error when trying to start sphinx and the file noted
there is not being generated. permission on the directory is for all.

(in C:/ror/rails\_apps/drug)  
searchd —config C:/ror/rails\_apps/drug/config/development.sphinx.conf  
Failed to start searchd daemon. Check
C:/ror/rails\_apps/drug/log/searchd.log.

</div>
<div class="comment-author">
<a href="http://freelancing-gods.com">pat</a> left a comment on 21 Dec,
2008:</div>

<div class="comment" markdown="1">
Not quite sure what could be stopping development.sphinx.conf from
generating… probably best to continue this discussion on the [google
group](http://groups.google.com/group/thinking-sphinx)

</div>
<div class="comment-author">
tashfeen left a comment on 22 Dec, 2008:</div>

<div class="comment" markdown="1">
ok, treid there too but no help yet. let me know if you come up with
anything. i am developing on windows if that may help…

</div>
<div class="comment-author">
<a href="http://semanticart.com">Jeff</a> left a comment on 6 Mar,
2009:</div>

<div class="comment" markdown="1">
Not trying to blog spam here, but for those who get here via google:
Don’t be discouraged by the integer primary key issue, it can be
overcome. See
http://blog.semanticart.com/comparing\_thinking\_sphinx\_and\_acts\_as\_ferret\_for\_full-text\_indexing\_in\_rails

</div>
</div>

