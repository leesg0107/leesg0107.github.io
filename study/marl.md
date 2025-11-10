---
layout: page
title: Multi-Agent RL
subtitle: Multi-agent systems and cooperative learning
permalink: /study/marl/
---

<div class="posts-list">
{% assign study_posts = site.posts | where: "category", "study" %}
{% assign marl_posts = study_posts | where_exp: "post", "post.tags contains 'MARL'" %}
{% if marl_posts.size > 0 %}
  {% for post in marl_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    {% if post.subtitle %}<p class="post-subtitle">{{ post.subtitle }}</p>{% endif %}
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  </article>
  {% endfor %}
{% else %}
  <p><em>No MARL posts yet.</em></p>
{% endif %}
</div>
