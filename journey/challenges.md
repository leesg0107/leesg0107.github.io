---
layout: page
title: "Challenges"
subtitle: "Life's obstacles, bold attempts, and the wisdom found in setbacks"
show-avatar: false
---

<div class="posts-list">
  {% assign challenge_posts = site.posts | where: "category", "journey" | where: "subcategory", "challenges" %}
  {% for post in challenge_posts %}
  <article class="post-preview">
    <a href="{{ post.url | relative_url }}">
      <h2 class="post-title">{{ post.title }}</h2>
      {% if post.subtitle %}
        <h3 class="post-subtitle">{{ post.subtitle }}</h3>
      {% endif %}
    </a>

    <p class="post-meta">
      {% assign date_format = site.date_format | default: "%B %-d, %Y" %}
      Posted on {{ post.date | date: date_format }}
      {% if post.tags.size > 0 %}
        <br>
        <span class="blog-tags">
          {% for tag in post.tags %}
            <a href="{{ '/tags' | relative_url }}#{{- tag -}}">#{{ tag }}</a>{% unless forloop.last %},{% endunless %}
          {% endfor %}
        </span>
      {% endif %}
    </p>

    <div class="post-entry-container">
      {% if post.image %}
        <div class="post-image">
          <a href="{{ post.url | relative_url }}">
            <img src="{{ post.image | relative_url }}" alt="{{ post.title }}">
          </a>
        </div>
      {% endif %}
      <div class="post-entry">
        {% assign excerpt_length = site.excerpt_length | default: 50 %}
        {{ post.excerpt | strip_html | xml_escape | truncatewords: excerpt_length }}
        {% assign excerpt_word_count = post.excerpt | number_of_words %}
        {% if post.content != post.excerpt or excerpt_word_count > excerpt_length %}
          <a href="{{ post.url | relative_url }}" class="post-read-more">[Read&nbsp;More]</a>
        {% endif %}
      </div>
    </div>
  </article>
  {% endfor %}
</div>

{% if challenge_posts.size == 0 %}
<div class="text-center">
  <h3>🎯 Life's Adventures</h3>
  <p>This is where I'll share the moments when I dared to try something new, failed spectacularly, or learned something unexpected about myself.</p>
  <p>Because in SOLIFE, every challenge is just another chapter in the story! 📖</p>
  <p><em>"The cave you fear to enter holds the treasure you seek."</em> - Let's explore together! 🗝️✨</p>
</div>
{% endif %} 