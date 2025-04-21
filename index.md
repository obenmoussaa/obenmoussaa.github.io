---
layout: default
title: Home
---

<div class="hero-banner">
  <h1>Welcome to My Cybersecurity Blog</h1>
  <p class="lead">Sharing security knowledge, CTF writeups, and lab walkthroughs</p>
</div>

## Recent Articles

<div class="post-list">
  {% for post in site.posts limit:3 %}
  <div class="post-card">
    <h3 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    {% if post.difficulty %}
    <span class="difficulty {{ post.difficulty }}">{{ post.difficulty }}</span>
    {% endif %}
    </h3>
    <div class="post-meta">{{ post.date | date: "%B %d, %Y" }}</div>
    <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
    <div class="tags">
      {% for tag in post.tags %}
      <span class="tag">{{ tag }}</span>
      {% endfor %}
    </div>
  </div>
  {% endfor %}
</div>

<h2 class="section-heading">Featured Content</h2>

<div class="featured-grid">
  <!-- Tutorials -->
  <div class="featured-card">
    <div class="featured-image">
      <i class="fas fa-book-open"></i>
    </div>
    <div class="featured-content">
      <h3 class="featured-title">Tutorials</h3>
      <ul>
        {% for tutorial in site.tutorials limit:3 %}
        <li><a href="{{ tutorial.url | relative_url }}">{{ tutorial.title }}</a></li>
        {% endfor %}
      </ul>
      <a href="{{ '/tutorials' | relative_url }}" class="view-all">View all tutorials →</a>
    </div>
  </div>
  
  <!-- Web Labs -->
  <div class="featured-card">
    <div class="featured-image">
      <i class="fas fa-spider"></i>
    </div>
    <div class="featured-content">
      <h3 class="featured-title">Web Security Labs</h3>
      <ul>
        {% for web in site.web limit:3 %}
        <li><a href="{{ web.url | relative_url }}">{{ web.title }}</a></li>
        {% endfor %}
      </ul>
      <a href="{{ '/web' | relative_url }}" class="view-all">View all web labs →</a>
    </div>
  </div>
  
  <!-- Tools -->
  <div class="featured-card">
    <div class="featured-image">
      <i class="fas fa-tools"></i>
    </div>
    <div class="featured-content">
      <h3 class="featured-title">Security Tools</h3>
      <p>Discover the tools and techniques I use for security testing and analysis.</p>
      <a href="{{ '/tools' | relative_url }}" class="view-all">Explore tools →</a>
    </div>
  </div>
</div>
