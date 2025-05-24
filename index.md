---
layout: default
title: Home
---

<h2>Latest Posts</h2>
<ul>
  {% for post in site.posts %}
    <li>
      <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
      <p class="post-meta">{{ post.date | date_to_string }}</p>
      {{ post.excerpt }}
      <a href="{{ post.url | relative_url }}">Read more</a>
    </li>
  {% endfor %}
</ul>

Some useful notes are keep here linked with obsidian.


