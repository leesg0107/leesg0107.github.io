---
layout: page
title: "Dev Log"
subtitle: "Coding adventures, debugging tales, and technical discoveries in my SOLIFE"
show-avatar: false
---

<div class="posts-list">
  {% assign dev_posts = site.posts | where: "category", "journey" | where: "subcategory", "dev" %}
  {% for post in dev_posts %}
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

{% if dev_posts.size == 0 %}
<div class="text-center">
  <h3>🚧 Coming Soon</h3>
  <p>This is where I'll document my daily coding life - from "why isn't this working?" moments to "finally got it!" victories.</p>
  <p>Real stories from the trenches of development, because coding is not just about the code - it's about the journey! 💻✨</p>
  <p><em>Code, debug, reflect, repeat - the SOLIFE way!</em> 🔄</p>
</div>
{% endif %} 