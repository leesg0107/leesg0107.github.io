---
layout: page
title: Robotics
subtitle: Robotics systems and applications
permalink: /study/robotics/
---

<div class="posts-list">
{% assign study_posts = site.posts | where: "category", "study" %}
{% assign robotics_posts = study_posts | where_exp: "post", "post.tags contains 'Robotics'" %}
{% if robotics_posts.size > 0 %}
  {% for post in robotics_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    {% if post.subtitle %}<p class="post-subtitle">{{ post.subtitle }}</p>{% endif %}
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  </article>
  {% endfor %}
{% else %}
  <p><em>No Robotics posts yet.</em></p>
{% endif %}
</div>
