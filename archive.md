---
layout: page
title: Archive
permalink: /archive/
---

Posts from 2016–2017, written while learning web development at Flatiron School. This blog from a decade ago is left here as a testament to the days before AI and how we used to write software with typing and stack overflow and deep googling.

<ul class="post-list">
  {% for post in site.posts %}
    {% assign post_year = post.date | date: "%Y" | plus: 0 %}
    {% if post_year <= 2017 %}
      <li>
        <div class="post-meta">
          <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %-d, %Y" }}</time>
          {% assign words = post.content | number_of_words %}
          {% if words < 200 %}{% assign minutes = 1 %}{% else %}{% assign minutes = words | divided_by: 200 %}{% endif %}
          <span class="reading-time">{{ minutes }} min read</span>
        </div>
        <h2><a class="post-link" href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
        {% if post.excerpt %}
          <p class="post-excerpt">{{ post.excerpt | strip_html | truncate: 160 }}</p>
        {% endif %}
      </li>
    {% endif %}
  {% endfor %}
</ul>
