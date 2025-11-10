---
layout: page
title: Reinforcement Learning
subtitle: Deep dive into RL algorithms and applications
permalink: /study/rl/
---

<div class="posts-list">
{% assign rl_posts = site.posts | where_exp: "post", "post.category == 'study' and post.tags contains 'RL'" %}
{% if rl_posts.size > 0 %}
  {% for post in rl_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    {% if post.subtitle %}<p class="post-subtitle">{{ post.subtitle }}</p>{% endif %}
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  </article>
  {% endfor %}
{% else %}
  <p><em>No RL posts yet.</em></p>
{% endif %}
</div>
