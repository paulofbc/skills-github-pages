---
layout: page
title: Categories
permalink: /categories/
description: Posts grouped by category.
---

{% assign sorted_categories = site.categories | sort %}
{% for cat in sorted_categories %}
## <a id="{{ cat[0] | slugify }}"></a>{{ cat[0] }} ({{ cat[1] | size }})

{% for post in cat[1] %}
- **[{{ post.title }}]({{ post.url | relative_url }})**
  &middot; <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%Y-%m-%d" }}</time>
  {% if post.description %}<br/>{{ post.description }}{% endif %}
{% endfor %}

{% endfor %}
