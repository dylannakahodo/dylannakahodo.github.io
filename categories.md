---
layout: page
permalink: /categories/
title: Categories
---


{%- assign date_format = site.date_format | default: "%B %d, %Y" -%}
{% for category in site.categories %}
  <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: date_format )}}</time> - <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
