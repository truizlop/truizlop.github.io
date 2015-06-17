---
layout: page
show_meta: false
title: "Miscellaneous"
subheadline: "Random stuff"
header:
   image_fullwidth: "header_misc.png"
permalink: "/misc/"
---
<ul>
    {% for post in site.categories.misc %}
    <li><a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>