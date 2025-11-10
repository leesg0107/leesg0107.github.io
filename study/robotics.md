---
layout: page
title: Robotics
subtitle: Robotics systems and applications
permalink: /study/robotics/
---

<div class="posts-list">
{% assign robotics_posts = site.posts | where_exp: "post", "post.category == 'study' and post.tags contains 'Robotics'" %}
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
