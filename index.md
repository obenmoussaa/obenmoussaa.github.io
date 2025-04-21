---
layout: default
title: Home
---

# Welcome to My Cybersecurity Blog

This blog contains writeups, tutorials, and explanations for various cybersecurity challenges and labs I've completed.

## Recent Posts

{% for post in site.posts limit:5 %}
- [{{ post.title }}]({{ post.url | relative_url }}) - {{ post.date | date: "%B %d, %Y" }}
{% endfor %}

## Featured Content

### Tutorials
{% for tutorial in site.tutorials limit:3 %}
- [{{ tutorial.title }}]({{ tutorial.url | relative_url }})
{% endfor %}
[View all tutorials](/tutorials)

### Web Security Labs
{% for web in site.web limit:3 %}
- [{{ web.title }}]({{ web.url | relative_url }})
{% endfor %}
[View all web labs](/web)
