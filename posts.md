---
layout: page
title: Posts
permalink: /posts/
description: All posts, newest first.
---

{% for post in site.posts %}
- **[{{ post.title }}]({{ post.url | relative_url }})**
  &middot; <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y-%m-%d" }}</time>
  {% if post.category %} &middot; [{{ post.category }}](/categories/#{{ post.category | slugify }}){% endif %}
  {% if post.description %}<br/>{{ post.description }}{% endif %}
{% endfor %}
