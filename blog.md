---
layout: page
title: Blog
subtitle: My thoughts and ideas
permalink: /blog/
---

<div class="container blog-container">
  <div class="posts-list">
    {% for post in site.posts %}
    <article class="card post-card mb-4">
      <div class="card-body">
        <a href="{{ post.url | relative_url }}">
          <h3 class="card-title">{{ post.title }}</h3>
          {% if post.subtitle %}
            <h4 class="card-subtitle text-muted">{{ post.subtitle }}</h4>
          {% endif %}
        </a>

        <p class="post-meta text-muted">
          Posted on {{ post.date | date: site.date_format }}
        </p>

        <div class="post-entry-container">
          {% if post.image %}
          <div class="post-image mb-3">
            <a href="{{ post.url | relative_url }}">
              <img src="{{ post.image | relative_url }}" class="img-fluid">
            </a>
          </div>
          {% endif %}
          <div class="post-entry">
            {{ post.excerpt | strip_html | xml_escape | truncatewords: site.excerpt_length }}
            {% assign excerpt_word_count = post.excerpt | number_of_words %}
            {% if post.content != post.excerpt or excerpt_word_count > site.excerpt_length %}
              <a href="{{ post.url | relative_url }}" class="post-read-more">[Read&nbsp;More]</a>
            {% endif %}
          </div>
        </div>

        {% if post.tags.size > 0 %}
        <div class="post-tags mt-3">
          Tags:
          {% for tag in post.tags %}
          <span class="post-tag badge">{{ tag }}</span>
          {% endfor %}
        </div>
        {% endif %}
      </div>
    </article>
    {% endfor %}
  </div>

  {% if paginator.total_pages > 1 %}
  <div class="pagination-container my-5">
    <ul class="pagination justify-content-center">
      {% if paginator.previous_page %}
      <li class="page-item previous">
        <a class="page-link" href="{{ paginator.previous_page_path | relative_url }}">&larr; Newer Posts</a>
      </li>
      {% endif %}
      {% if paginator.next_page %}
      <li class="page-item next">
        <a class="page-link" href="{{ paginator.next_page_path | relative_url }}">Older Posts &rarr;</a>
      </li>
      {% endif %}
    </ul>
  </div>
  {% endif %}
  
  <div class="card feature-card mb-5">
    <div class="card-body text-center">
      <h2 class="card-title"><i class="fas fa-envelope text-primary mr-2"></i> Connect With Me</h2>
      <p>I'm always interested in connecting with fellow developers, potential collaborators, or anyone interested in mobile development and technology.</p>
      <p>Check out my <a href="/projects/" class="link-highlight">projects</a> to see some of my work, or reach out directly if you'd like to chat!</p>
      <div class="connect-buttons mt-4">
        <a href="mailto:skubisz.rafael@gmail.com" class="btn btn-primary">
          <i class="fas fa-envelope me-2"></i> Email Me
        </a>
        <a href="https://github.com/Jenovas" class="btn btn-outline-primary mx-2" target="_blank">
          <i class="fab fa-github me-2"></i> GitHub
        </a>
        <a href="https://www.linkedin.com/in/rafael-skubisz-210993b0/" class="btn btn-outline-primary" target="_blank">
          <i class="fab fa-linkedin me-2"></i> LinkedIn
        </a>
      </div>
    </div>
  </div>
</div>

<style>
  .blog-container {
    max-width: 960px;
    margin: 0 auto;
  }
  
  .post-card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    border: 1px solid var(--dark-border);
    background-color: var(--dark-surface);
    cursor: pointer;
  }
  
  .post-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 15px rgba(0, 0, 0, 0.2);
    border-color: var(--primary-color);
  }
  
  .post-card .card-title {
    font-size: 1.5rem;
    margin-bottom: 0.5rem;
    transition: color 0.2s ease;
  }
  
  .post-card:hover .card-title {
    color: var(--primary-color);
  }
  
  .post-card a {
    text-decoration: none;
  }
  
  .card-subtitle {
    font-size: 1.1rem;
    margin-bottom: 1rem;
  }
  
  .post-meta {
    font-size: 0.9rem;
    margin-bottom: 1rem;
  }
  
  .post-entry {
    margin-bottom: 1rem;
    line-height: 1.6;
    color: var(--text-secondary);
  }
  
  .post-read-more {
    display: inline-block;
    margin-top: 0.5rem;
    color: var(--primary-color);
    font-weight: 500;
  }
  
  .post-read-more:hover {
    color: var(--primary-hover);
    text-decoration: underline;
  }
  
  .post-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.5rem;
    align-items: center;
  }
  
  .post-tag {
    background-color: var(--dark-bg);
    color: var(--primary-color);
    border: 1px solid var(--primary-color);
    padding: 0.4rem 0.7rem;
    font-size: 0.8rem;
    font-weight: 400;
    border-radius: 4px;
  }
  
  .page-link {
    background-color: var(--dark-surface);
    border-color: var(--dark-border);
    color: var(--primary-color);
  }
  
  .page-link:hover {
    background-color: var(--primary-color);
    border-color: var(--primary-color);
    color: var(--dark-surface);
  }
  
  .feature-card {
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    border: 1px solid var(--dark-border);
  }
  
  .feature-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 15px rgba(0, 0, 0, 0.2);
    border-color: var(--primary-color);
  }
  
  .card-title {
    display: flex;
    align-items: center;
  }
  
  .feature-card .card-title {
    display: flex;
    align-items: center;
  }
  
  .card-title i {
    margin-right: 0.5rem;
  }
  
  .link-highlight {
    color: var(--primary-color);
    font-weight: 500;
    text-decoration: none;
    border-bottom: 1px dotted var(--primary-color);
    transition: all 0.2s ease;
  }
  
  .link-highlight:hover {
    color: var(--primary-hover);
    border-bottom: 1px solid var(--primary-hover);
  }
  
  .connect-buttons {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 0.5rem;
  }
  
  @media (max-width: 767px) {
    .connect-buttons {
      flex-direction: column;
      align-items: center;
    }
    
    .connect-buttons .btn {
      margin: 0.5rem 0;
      width: 100%;
    }
  }
</style> 