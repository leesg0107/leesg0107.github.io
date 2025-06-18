---
layout: page
title: Projects
subtitle: Innovative projects and development logs
permalink: /projects/
---

<div class="projects-header">
  <div class="header-icon">
    <i class="fas fa-code-branch"></i>
  </div>
  <div class="header-info">
    <h1>My Projects</h1>
    <p>Exploring innovative solutions in AI, Graph Theory, and Software Development</p>
  </div>
</div>

<div class="projects-container">
  {% assign project_posts = site.posts | where: "category", "project" %}
  {% if project_posts.size > 0 %}
    <div class="projects-grid">
      {% for project in project_posts %}
        <article class="project-card">
          {% if project.thumbnail-img %}
          <div class="project-image">
            <img src="{{ project.thumbnail-img | relative_url }}" alt="{{ project.title }}">
            <div class="project-overlay">
              <a href="{{ project.url | relative_url }}" class="project-link">
                <i class="fas fa-external-link-alt"></i>
              </a>
            </div>
          </div>
          {% endif %}
          
          <div class="project-content">
            <div class="project-meta">
              <span class="project-date">
                <i class="fas fa-calendar"></i>
                {{ project.date | date: "%B %Y" }}
              </span>
              {% if project.author %}
                <span class="project-author">
                  <i class="fas fa-user"></i>
                  {{ project.author }}
                </span>
              {% endif %}
            </div>
            
            <h3 class="project-title">
              <a href="{{ project.url | relative_url }}">{{ project.title }}</a>
            </h3>
            
            {% if project.subtitle %}
            <p class="project-subtitle">{{ project.subtitle }}</p>
            {% endif %}
            
            <p class="project-description">{{ project.excerpt | strip_html | truncatewords: 30 }}</p>
            
            {% if project.tags %}
            <div class="project-tags">
              {% for tag in project.tags %}
                <span class="project-tag">{{ tag }}</span>
              {% endfor %}
            </div>
            {% endif %}
            
            <div class="project-actions">
              <a href="{{ project.url | relative_url }}" class="btn-project">
                Read More <i class="fas fa-arrow-right"></i>
              </a>
            </div>
          </div>
        </article>
      {% endfor %}
    </div>
  {% else %}
    <div class="no-projects">
      <i class="fas fa-folder-open"></i>
      <h3>No projects yet</h3>
      <p>Check back soon for exciting project updates!</p>
    </div>
  {% endif %}
</div>

<style>
.projects-header {
  display: flex;
  align-items: center;
  gap: 30px;
  margin-bottom: 50px;
  padding: 40px;
  background: linear-gradient(135deg, #43e97b 0%, #38f9d7 100%);
  border-radius: 20px;
  color: white;
  box-shadow: 0 20px 40px rgba(67, 233, 123, 0.3);
}

.header-icon {
  font-size: 4rem;
  opacity: 0.9;
}

.header-info h1 {
  margin: 0 0 10px 0;
  font-size: 2.5rem;
  font-weight: 700;
}

.header-info p {
  margin: 0;
  font-size: 1.1rem;
  opacity: 0.9;
}

.projects-container {
  margin-top: 40px;
}

.projects-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 30px;
}

.project-card {
  background: white;
  border-radius: 20px;
  box-shadow: 0 15px 35px rgba(0,0,0,0.1);
  overflow: hidden;
  transition: all 0.3s ease;
  border: 1px solid #f0f0f0;
  position: relative;
}

.project-card:hover {
  transform: translateY(-10px);
  box-shadow: 0 25px 50px rgba(0,0,0,0.2);
}

.project-image {
  height: 250px;
  position: relative;
  overflow: hidden;
}

.project-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
}

.project-card:hover .project-image img {
  transform: scale(1.1);
}

.project-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(67, 233, 123, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.project-card:hover .project-overlay {
  opacity: 1;
}

.project-link {
  color: white;
  font-size: 2rem;
  text-decoration: none;
  transition: transform 0.3s ease;
}

.project-link:hover {
  transform: scale(1.2);
  color: white;
}

.project-content {
  padding: 30px;
}

.project-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  font-size: 0.9rem;
  color: #718096;
}

.project-meta span {
  display: flex;
  align-items: center;
  gap: 5px;
}

.project-title {
  margin: 0 0 10px 0;
  font-size: 1.5rem;
  font-weight: 700;
}

.project-title a {
  color: #2d3748;
  text-decoration: none;
  transition: color 0.3s ease;
}

.project-title a:hover {
  color: #43e97b;
}

.project-subtitle {
  color: #4a5568;
  font-style: italic;
  margin-bottom: 15px;
  font-size: 1rem;
}

.project-description {
  color: #718096;
  line-height: 1.6;
  margin-bottom: 20px;
}

.project-tags {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-bottom: 25px;
}

.project-tag {
  background: linear-gradient(135deg, #43e97b, #38f9d7);
  color: white;
  padding: 5px 12px;
  border-radius: 15px;
  font-size: 0.8rem;
  font-weight: 500;
}

.project-actions {
  text-align: center;
}

.btn-project {
  background: linear-gradient(135deg, #43e97b, #38f9d7);
  color: white;
  padding: 12px 25px;
  border-radius: 25px;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.3s ease;
  display: inline-block;
}

.btn-project:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(67, 233, 123, 0.3);
  color: white;
}

.no-projects {
  text-align: center;
  padding: 80px 20px;
  color: #718096;
}

.no-projects i {
  font-size: 4rem;
  margin-bottom: 20px;
  opacity: 0.5;
}

.no-projects h3 {
  margin-bottom: 10px;
  color: #4a5568;
}

@media (max-width: 768px) {
  .projects-header {
    flex-direction: column;
    text-align: center;
    gap: 20px;
    padding: 30px 20px;
  }
  
  .header-icon {
    font-size: 3rem;
  }
  
  .header-info h1 {
    font-size: 2rem;
  }
  
  .projects-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }
  
  .project-content {
    padding: 20px;
  }
  
  .project-meta {
    flex-direction: column;
    align-items: flex-start;
    gap: 5px;
  }
}
</style>
