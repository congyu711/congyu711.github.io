---
layout: page
title: categories
---

<div class="tags-expo">
    <div class="tags-expo-list">
      {% for tag in site.categories %}
      <a href="#{{ site.categories[0] }}" class="post-tag">{{ site.categories[0] }}</a>
      {% endfor %}
    </div>
    <hr/>
    <div class="tags-expo-section">
      {% for tag in site.categories %}
      <h3 id="{{ tag[0] | slugify }}">{{ tag | first }}</h3>
      <ul class="tags-expo-posts">
         {%- for post in tag[1] -%}
      <li>
        {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
        <span class="post-meta">{{ post.date | date: date_format }}</span>
        <h3>
          <a class="post-link" href="{{ post.url | relative_url }}">
            {{ post.title | escape }}
          </a>
        </h3>
        {%- if site.show_excerpts -%}
          {{ post.excerpt }}
        {%- endif -%}
      </li>
      {%- endfor -%}
      </ul>
      {% endfor %}
    </div>
  </div>
