---
layout: page
title: Tags
---

<div align="center">
    {% for tag in site.tags %}
    <a href="/tags/#{{ tag[0] }}"
    style="font-size: {{ tag[1] | size | times: 2 | plus: 10 }}px">
        {{ tag[0] }} |
    </a>
    {% endfor %}
    
</div>

<div>
{% for tag in site.tags %}
	<h2 id="{{ tag[0] | slugify }}">{{ tag[0] }}</h2>
	<ul>
	 {% for post in site.posts %}
		 {% if post.tags contains tag[0] %}
		 <li>
		 <h3>
		 <a href="{{ post.url }}">
		 {{ post.title }}
		 </a>
		 </h3>
		 </li>
		 {% endif %}
	 {% endfor %}
	</ul>
{% endfor %}
</div>