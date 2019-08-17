---
layout: page
title: Jack Jack Attack!
tagline: Web Incredibles
---
{% include JB/setup %}

Welcome to visit the site of Jack Jack Attack! As a software engineer at <a href="https://www.castlighthealth.com/">Castlight Health Inc</a>, my blog here includes some discussions in certain web development topics, such as JavaScript, Ruby and Rails. Thanks for coming and looking forward to have your tweets on <a href="https://twitter.com/?status=@xjlin0">@xjlin0</a> or your comments on disqus.

### Some of my thoughts:

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>