---
layout: page
title: Blog Archive
---

{% for tag in site.tags %}
  <h3>{{ tag[0] }}</h3>
  <ul class="post-list">
    {% for post in tag[1] %}
  <li>
    <span class="post-meta">{{ post.date | date: "%B %Y" }}</span>
    <h3>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </h3>
  </li>
    {% endfor %}
  </ul>
{% endfor %}
