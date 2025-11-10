---
layout: page
title: Challenges
subtitle: Overcoming obstacles and learning experiences
permalink: /journey/challenges/
---

<div class="posts-list">
{% assign challenge_posts = site.posts | where_exp: "post", "post.category == 'journey' and post.tags contains 'challenges'" %}
{% if challenge_posts.size > 0 %}
  {% for post in challenge_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    {% if post.subtitle %}<p class="post-subtitle">{{ post.subtitle }}</p>{% endif %}
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  </article>
  {% endfor %}
{% else %}
  <p><em>No challenge posts yet.</em></p>
{% endif %}
</div>
