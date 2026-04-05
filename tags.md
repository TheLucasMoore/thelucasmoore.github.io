---
layout: page
title: Tags
permalink: /tags/
---

<div class="tag-cloud">
  {% assign sorted_tags = site.tags | sort %}
  {% for tag in sorted_tags %}
    <a class="tag-pill" href="{{ '/tags/' | append: tag[0] | relative_url }}">
      {{ tag[0] }} ({{ tag[1].size }})
    </a>
  {% endfor %}
</div>
