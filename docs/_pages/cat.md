---
layout: page
title: categories
---
<!-- solution from https://stackoverflow.com/questions/20872861/jekyll-display-posts-by-category -->
<ul>
{% for category in site.categories %}
  <li>{{ category | first }}
    <ul>
    {% for post in category.last %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
    </ul>
  </li>
{% endfor %}
</ul>