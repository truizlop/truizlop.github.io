---
layout: page
show_meta: false
title: "Books"
subheadline: "Book reviews"
header:
   image_fullwidth: "header_books.png"
permalink: "/books/"
---
<ul>
    {% for post in site.categories.books %}
    <li><a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>