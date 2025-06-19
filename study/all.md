---
layout: page
title: All Studies
subtitle: Complete collection of research notes and insights
permalink: /study/all/
---

<div class="study-category-header all-studies-header">
  <div class="category-icon">
    <i class="fas fa-book-open"></i>
  </div>
  <div class="category-info">
    <h1>All Studies</h1>
    <p>Comprehensive collection of research across RL, MARL, Robotics, and more</p>
  </div>
</div>

<!-- Category Filter -->
<div class="category-filter">
  <button class="filter-btn active" data-category="all">All</button>
  <button class="filter-btn" data-category="RL">RL</button>
  <button class="filter-btn" data-category="MARL">MARL</button>
  <button class="filter-btn" data-category="Robotics">Robotics</button>
  <button class="filter-btn" data-category="GNN">GNN</button>
  <button class="filter-btn" data-category="HR">HR</button>
  <button class="filter-btn" data-category="MA">MA</button>
</div>

<div class="study-posts-container">
  {% assign study_posts = site.posts | where: "category", "study" %}
  {% if study_posts.size > 0 %}
    <div class="posts-grid">
      {% for post in study_posts %}
        <article class="study-post-card" data-tags="{{ post.tags | join: ',' }}">
          {% if post.thumbnail-img %}
          <div class="post-image">
            <img src="{{ post.thumbnail-img | relative_url }}" alt="{{ post.title }}">
          </div>
          {% endif %}
          <div class="post-content">
            <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
            <p class="post-meta">
              <i class="fas fa-calendar"></i> {{ post.date | date: "%B %d, %Y" }}
              {% if post.author %}
                <span class="author">by {{ post.author }}</span>
              {% endif %}
            </p>
            <p class="post-excerpt">{{ post.excerpt | strip_html | truncatewords: 25 }}</p>
            {% if post.tags %}
            <div class="post-tags">
              {% for tag in post.tags %}
                <span class="tag">{{ tag }}</span>
              {% endfor %}
            </div>
            {% endif %}
            <a href="{{ post.url | relative_url }}" class="read-more">Read More <i class="fas fa-arrow-right"></i></a>
          </div>
        </article>
      {% endfor %}
    </div>
  {% else %}
    <div class="no-posts">
      <i class="fas fa-book"></i>
      <h3>No study posts yet</h3>
      <p>Check back soon for exciting research content!</p>
    </div>
  {% endif %}
</div>

<style>
.all-studies-header {
  background: linear-gradient(135deg, #a29bfe 0%, #6c5ce7 100%);
}

.study-category-header {
  display: flex;
  align-items: center;
  gap: 30px;
  margin-bottom: 40px;
  padding: 30px;
  border-radius: 20px;
  color: white;
}

.category-icon {
  font-size: 4rem;
  opacity: 0.9;
}

.category-info h1 {
  margin: 0 0 10px 0;
  font-size: 2.5rem;
  font-weight: 700;
}

.category-info p {
  margin: 0;
  font-size: 1.1rem;
  opacity: 0.9;
}

.category-filter {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-bottom: 30px;
  justify-content: center;
}

.filter-btn {
  background: #f8fafc;
  border: 2px solid #e2e8f0;
  color: #4a5568;
  padding: 10px 20px;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 500;
}

.filter-btn:hover {
  background: #e2e8f0;
  color: #2d3748;
}

.filter-btn.active {
  background: linear-gradient(135deg, #a29bfe, #6c5ce7);
  color: white;
  border-color: #6c5ce7;
}

.study-posts-container {
  margin-top: 40px;
}

.posts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 30px;
}

.study-post-card {
  background: white;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0,0,0,0.1);
  overflow: hidden;
  transition: transform 0.3s ease, box-shadow 0.3s ease, opacity 0.3s ease;
  border: 1px solid #f0f0f0;
}

.study-post-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 20px 40px rgba(0,0,0,0.15);
}

.study-post-card.hidden {
  display: none;
}

.post-image {
  height: 200px;
  overflow: hidden;
}

.post-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.study-post-card:hover .post-image img {
  transform: scale(1.05);
}

.post-content {
  padding: 25px;
}

.post-content h3 {
  margin: 0 0 15px 0;
  font-size: 1.4rem;
  font-weight: 600;
}

.post-content h3 a {
  color: #2d3748;
  text-decoration: none;
  transition: color 0.3s ease;
}

.post-content h3 a:hover {
  color: #a29bfe;
}

.post-meta {
  color: #718096;
  font-size: 0.9rem;
  margin-bottom: 15px;
  display: flex;
  align-items: center;
  gap: 10px;
}

.post-meta .author {
  margin-left: 10px;
}

.post-excerpt {
  color: #4a5568;
  line-height: 1.6;
  margin-bottom: 20px;
}

.post-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 20px;
}

.tag {
  background: #e2e8f0;
  color: #4a5568;
  padding: 4px 12px;
  border-radius: 15px;
  font-size: 0.8rem;
  font-weight: 500;
}

.read-more {
  color: #a29bfe;
  text-decoration: none;
  font-weight: 500;
  transition: color 0.3s ease;
}

.read-more:hover {
  color: #6c5ce7;
}

.no-posts {
  text-align: center;
  padding: 60px 20px;
  color: #718096;
}

.no-posts i {
  font-size: 4rem;
  margin-bottom: 20px;
  opacity: 0.5;
}

.no-posts h3 {
  margin-bottom: 10px;
  color: #4a5568;
}

@media (max-width: 768px) {
  .study-category-header {
    flex-direction: column;
    text-align: center;
    gap: 20px;
  }
  
  .category-icon {
    font-size: 3rem;
  }
  
  .category-info h1 {
    font-size: 2rem;
  }
  
  .posts-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }
  
  .category-filter {
    justify-content: flex-start;
  }
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const filterBtns = document.querySelectorAll('.filter-btn');
  const postCards = document.querySelectorAll('.study-post-card');
  
  filterBtns.forEach(btn => {
    btn.addEventListener('click', function() {
      // Remove active class from all buttons
      filterBtns.forEach(b => b.classList.remove('active'));
      // Add active class to clicked button
      this.classList.add('active');
      
      const category = this.getAttribute('data-category');
      
      postCards.forEach(card => {
        const tags = card.getAttribute('data-tags');
        
        if (category === 'all') {
          card.classList.remove('hidden');
        } else {
          if (tags && tags.includes(category)) {
            card.classList.remove('hidden');
          } else {
            card.classList.add('hidden');
          }
        }
      });
    });
  });
});
</script> 