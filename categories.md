---
layout: page
permalink: /categories/
title: Categories
---


<div id="archives">
{%- assign date_format = site.date_format | default: "%B %d, %Y" -%}
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>

    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>
    {% for post in site.categories[category_name] %}
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: date_format )}}</time>
    <article class="archive-item">
        <a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>