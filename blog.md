---
layout: page
title: Blog
subtitle: My thoughts and ideas
eyebrow: "WRITING"
permalink: /blog/
---

<section class="section-block container">
  <span class="section-block__num">§ 01</span>
  <h2 class="section-block__title">Posts</h2>
  <hr class="section-block__rule">

  {% for post in site.posts %}
  <a class="card card--linked post-card" href="{{ post.url | relative_url }}">
    <div class="card-body">
      <p class="post-card__meta">{{ post.date | date: "%b %d %Y" | upcase }}</p>
      <h3 class="card-title">{{ post.title }}</h3>
      <p class="card-text">{{ post.excerpt | strip_html | truncatewords: 40 }}</p>
      {% if post.tags.size > 0 %}
      <div class="post-card__tags">
        {% for tag in post.tags %}
        <span class="tag tag--signal">{{ tag }}</span>
        {% endfor %}
      </div>
      {% endif %}
      <span class="btn-link-arrow mt-auto">Read more <span class="arrow">→</span></span>
    </div>
  </a>
  {% endfor %}
</section>
