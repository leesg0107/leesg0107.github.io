---
layout: page
title: Dev Log
subtitle: Development journey and technical insights
permalink: /journey/dev/
---

<div class="posts-list">
{% assign dev_posts = site.posts | where_exp: "post", "post.category == 'journey' and post.tags contains 'dev'" %}
{% if dev_posts.size > 0 %}
  {% for post in dev_posts %}
  <article>
    <h2 class="post-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
    {% if post.subtitle %}<p class="post-subtitle">{{ post.subtitle }}</p>{% endif %}
    <p class="post-meta">{{ post.date | date: "%B %d, %Y" }}</p>
  </article>
  {% endfor %}
{% else %}
  <p><em>No dev log posts yet.</em></p>
{% endif %}
</div>
