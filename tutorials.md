---
layout: default
title: Tutoriels
permalink: /tutorials/
---

# Tutoriels

Découvrez mes tutoriels sur les outils et techniques de cybersécurité.

{% assign sorted_tutorials = site.tutorials | sort: 'date' | reverse %}
{% for tutorial in sorted_tutorials %}
<div class="post-card">
  <h3 class="post-title">
    <a href="{{ tutorial.url | relative_url }}">{{ tutorial.title }}</a>
  </h3>
  <div class="post-meta">{{ tutorial.date | date: "%B %d, %Y" }}</div>
  <p>{{ tutorial.excerpt | strip_html | truncatewords: 30 }}</p>
  <div class="tags">
    {% for tag in tutorial.tags %}
    <span class="tag">{{ tag }}</span>
    {% endfor %}
  </div>
</div>
{% endfor %}