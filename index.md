---
layout: default
title: Home
---

# Welcome to Blogaphony

{% for post in site.posts %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.date | date_to_string }}</p>
  {{ post.excerpt }}
{% endfor %}
---
layout: home
---

<h2>Latest Posts</h2>

<ul>
  {% for post in site.posts %}
    <li>
      <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
      <p>{{ post.date | date_to_string }}</p>
      <p>{{ post.excerpt }}</p>
    </li>
  {% endfor %}
</ul>
