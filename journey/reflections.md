---
layout: page
title: Reflections
subtitle: Thoughts and meaningful moments
permalink: /journey/reflections/
---

<div class="posts-list">
{% assign journey_posts = site.posts | where: "category", "journey" %}
{% assign reflection_posts = journey_posts | where_exp: "post", "post.tags contains 'reflections'" %}
{% if reflection_posts.size > 0 %}
  {% for post in reflection_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    {% if post.subtitle %}<p class="post-subtitle">{{ post.subtitle }}</p>{% endif %}
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  </article>
  {% endfor %}
{% else %}
  <p><em>No reflection posts yet.</em></p>
{% endif %}
</div>
