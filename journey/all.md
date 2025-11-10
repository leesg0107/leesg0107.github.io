---
layout: page
title: Journey
subtitle: Daily insights, dev logs, and life lessons
permalink: /journey/all/
---

<div class="posts-list">
{% assign journey_posts = site.posts | where: "category", "journey" %}
{% if journey_posts.size > 0 %}
  {% for post in journey_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    
    {% if post.subtitle %}
    <p class="post-subtitle">{{ post.subtitle }}</p>
    {% endif %}
    
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
    
    {% if post.tags %}
    <div class="post-tags">
      {% for tag in post.tags limit: 5 %}
      <span class="post-tag">{{ tag }}</span>
      {% endfor %}
    </div>
    {% endif %}
  </article>
  {% endfor %}
{% else %}
  <p><em>Coming Soon! This is where I'll share the real stories behind SOLIFE.</em></p>
{% endif %}
</div>
