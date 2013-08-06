---
layout: page
title: Hi, I'm Johannes.
tagline: I do stuff.
---

![hail eris](/assets/asdf.png)

Occasionally, I also blog.

{% for post in site.posts %}
* {{ post.date | date_to_string }} » [{{ post.title }}]({{ post.url }})
{% endfor %} 
