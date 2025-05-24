---
layout: default
title: Notes
---

# Notes

## All Posts

<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>: {{ post.date | date: "%d %b %Y" }}
      {% if post.excerpt and post.excerpt != "" %}
      <p style="margin-top: 0.5em; margin-bottom: 1em;">{{ post.excerpt }}</p>
      {% endif %}
    </li>
  {% endfor %}
</ul>


