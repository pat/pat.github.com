---
title: "The Quest for Semantic Scrolling Tables"
redirect_from: "/posts/the_quest_for_semantic_scrolling_tables/"
categories:
  - tables
  - html
  - javascript
  - scrolling
---
At the start of this week, I was hunting around the internet for a
scrolling table solution with the following requirements:

-   No extra markup
-   Cross-browser including IE
-   Ideally just HTML and CSS

Unfortunately, there doesn’t seem to be any solutions that fit those
criteria. So, with some javascript (yes, that doesn’t fit the last
item), I built my own.

### Examples

Check out the
[before](http://freelancing-gods.com/examples/scroll_table/before.html)
and
[after](http://freelancing-gods.com/examples/scroll_table/after.html)
examples, and the [javascript file
itself](http://freelancing-gods.com/examples/scroll_table/scroll_table.js)
- unfortunately the current version is built on top of
[prototype](http://prototypejs.org), but if you feel like adapting it to
not use any external libraries, be my guest.

### Usage

The table must have a `thead` element specified - this is the section
that won’t scroll - and include the CSS class ‘scroll’ in the `table`
element. To have an enforced size for the table (if you want it to
scroll, you need this), add something to your CSS specifying height for
elements with the class ‘scroll-block’. As long as prototype and the
scroll\_table js files are included, the tables will automatically
become scrollable as the page loads.

### How it Works

The original table is put into a new div which has the class
‘scroll-block’. The `thead` from the table is duplicated into a new
table above that div element, and the original table’s margin-top is set
to a negative value to hide its header. Then each new header cell has
its width set inline to force the columns to line up. It will fix these
up whenever the browser window is resized, too.

The resulting source isn’t ideal, but it works, and it means you don’t
have to do much to your nice, clean semantic tables. For anyone with
Javascript disabled, they just get a nice big table.

### One Minor Issue

Left borders on the table disappear - so if that is important, just add
it to the `div.scroll-block` element via CSS.

------------------------------------------------------------------------

<div class="comments">
<div class="comment-author">
<a href="http://www.aestheticallyloyal.com">Kolber</a> left a comment on
15 Oct, 2007:</div>

<div class="comment" markdown="1">
I did notice that the thead breaks it’s alignment with the tbody on text
resize. This is due to a fixed width on the tbody I assume. You could
use the measurements in ems to try get around this issue.

You might also want to consider <pre>this.div.style.overflow =
“auto”;</pre> so that scrollbars only show when and where necessary.

</div>
<div class="comment-author">
<a href="http://germanforblack.com/">Ben Schwarz</a> left a comment on
15 Oct, 2007:</div>

<div class="comment" markdown="1">
You may want to make a heading for **examples** - a little hard to scan
when you’re looking for ‘what is this?’

Anyway, the only productive comment that I have is that you could add an
`overflow-x` (CSS) or `overflowX` (JS) property to the element and set
it to hidden.

Thus, no bottom scroller.

</div>
</div>

