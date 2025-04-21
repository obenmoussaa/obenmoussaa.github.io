---
layout: default
title: Web
permalink: /web/
---

# Web Labs

{% assign sorted_web = site.web | sort: 'date' | reverse %}
{% for web in sorted_web %}
<div class="post-card">
  <h3 class="post-title">
    <a href="{{ web.url | relative_url }}">{{ web.title }}</a>
  </h3>
  <div class="post-meta">{{ web.date | date: "%B %d, %Y" }}</div>
  <p>{{ web.excerpt | strip_html | truncatewords: 30 }}</p>
  <div class="tags">
    {% for tag in web.tags %}
    <span class="tag">{{ tag }}</span>
    {% endfor %}
  </div>
</div>
{% endfor %}