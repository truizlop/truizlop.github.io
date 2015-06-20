---
layout: page
show_meta: false
title: "Development"
subheadline: "Software development articles"
header:
   image_fullwidth: "header_development.png"
permalink: "/development/"
---
<ul>
    {% for post in site.categories.development %}
    <li><a href="{{ site.url }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>