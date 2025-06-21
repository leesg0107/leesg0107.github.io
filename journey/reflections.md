---
layout: page
title: "Reflections"
subtitle: "Life lessons, random thoughts, and those quiet moments of understanding"
show-avatar: false
---

<div class="posts-list">
  {% assign reflection_posts = site.posts | where: "category", "journey" | where: "subcategory", "reflections" %}
  {% for post in reflection_posts %}
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

{% if reflection_posts.size == 0 %}
<div class="text-center">
  <h3>🤔 Quiet Moments</h3>
  <p>This is where I capture those random thoughts that pop up during daily life - shower thoughts, commute insights, and late-night realizations.</p>
  <p>Sometimes the best lessons come from the most ordinary moments in our SOLIFE journey! 🌅</p>
  <p><em>"Life can only be understood backwards; but it must be lived forwards."</em> - Kierkegaard ⏰</p>
</div>
{% endif %} 