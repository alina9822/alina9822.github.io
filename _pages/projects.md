---
layout: page
title: Projects
permalink: /projects/
description: A growing collection of my academic and independent projects.
nav: true
nav_order: 5
display_categories: [work, fun]
horizontal: false
---

<!-- pages/projects.md -->
<style>
  /* the gem's projects.liquid card doesn't have a slot for a tools list,
     so this page renders its own card markup with one added */
  .projects .tool-badge {
    display: inline-block;
    background: color-mix(in srgb, var(--global-theme-color) 12%, transparent);
    color: var(--global-theme-color);
    border-radius: 0.25rem;
    padding: 0.15rem 0.5rem;
    font-size: 0.75rem;
    font-weight: 500;
    margin: 0 0.3rem 0.3rem 0;
  }

  .projects .project-tools {
    margin-bottom: 0.5rem;
  }

  .projects .project-code-link {
    display: inline-flex;
    align-items: center;
    gap: 0.4rem;
    margin: 0 0 0.75rem 0;
    padding: 0.3rem 0.75rem;
    font-size: 0.8rem;
    border: 1px solid var(--global-theme-color);
    border-radius: 0.25rem;
    color: var(--global-theme-color);
    background: transparent;
  }

  .projects .project-code-link:hover {
    background: color-mix(in srgb, var(--global-theme-color) 12%, transparent);
    color: var(--global-theme-color);
  }

  .projects .project-code-link i {
    font-size: 0.9rem;
  }
</style>

<div class="projects">
{% if site.enable_project_categories and page.display_categories %}
  <!-- Display categorized projects -->
  {% for category in page.display_categories %}
  <a id="{{ category }}" href=".#{{ category }}">
    <h2 class="category">{{ category }}</h2>
  </a>
  {% assign categorized_projects = site.projects | where: "category", category %}
  {% assign sorted_projects = categorized_projects | sort: "importance" %}
  <!-- Generate cards for each project -->
  {% if page.horizontal %}
  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      <div class="col">
        <div class="card h-100 hoverable">
          <a href="{% if project.redirect %}{{ project.redirect }}{% else %}{{ project.url | relative_url }}{% endif %}">
            {% if project.img %}
              {% include figure.liquid loading="eager" path=project.img sizes="250px" alt="project thumbnail" class="card-img-top" %}
            {% endif %}
          </a>
          <div class="card-body">
            <h2 class="card-title">
              <a href="{% if project.redirect %}{{ project.redirect }}{% else %}{{ project.url | relative_url }}{% endif %}">{{ project.title }}</a>
            </h2>
            {% if project.tools %}
              <p class="project-tools">
                {% for tool in project.tools %}<span class="tool-badge">{{ tool }}</span>{% endfor %}
              </p>
            {% endif %}
            {% if project.github_frontend or project.github_backend %}
              {% if project.github_frontend %}
                <a class="project-code-link" href="{{ project.github_frontend }}" target="_blank" rel="noopener noreferrer">
                  <i class="fa-brands fa-github"></i> Frontend
                </a>
              {% endif %}
              {% if project.github_backend %}
                <a class="project-code-link" href="{{ project.github_backend }}" target="_blank" rel="noopener noreferrer">
                  <i class="fa-brands fa-github"></i> Backend
                </a>
              {% endif %}
            {% elsif project.github %}
              <a class="project-code-link" href="{{ project.github }}" target="_blank" rel="noopener noreferrer">
                <i class="fa-brands fa-github"></i> Code
              </a>
            {% endif %}
            <p class="card-text">{{ project.description }}</p>
          </div>
        </div>
      </div>
    {% endfor %}
  </div>
  {% endif %}
  {% endfor %}

{% else %}

<!-- Display projects without categories -->

{% assign sorted_projects = site.projects | sort: "importance" %}

  <!-- Generate cards for each project -->

{% if page.horizontal %}

  <div class="container">
    <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      {% include projects_horizontal.liquid %}
    {% endfor %}
    </div>
  </div>
  {% else %}
  <div class="row row-cols-1 row-cols-md-2">
    {% for project in sorted_projects %}
      <div class="col">
        <div class="card h-100 hoverable">
          <a href="{% if project.redirect %}{{ project.redirect }}{% else %}{{ project.url | relative_url }}{% endif %}">
            {% if project.img %}
              {% include figure.liquid loading="eager" path=project.img sizes="250px" alt="project thumbnail" class="card-img-top" %}
            {% endif %}
          </a>
          <div class="card-body">
            <h2 class="card-title">
              <a href="{% if project.redirect %}{{ project.redirect }}{% else %}{{ project.url | relative_url }}{% endif %}">{{ project.title }}</a>
            </h2>
            {% if project.tools %}
              <p class="project-tools">
                {% for tool in project.tools %}<span class="tool-badge">{{ tool }}</span>{% endfor %}
              </p>
            {% endif %}
            {% if project.github_frontend or project.github_backend %}
              {% if project.github_frontend %}
                <a class="project-code-link" href="{{ project.github_frontend }}" target="_blank" rel="noopener noreferrer">
                  <i class="fa-brands fa-github"></i> Frontend
                </a>
              {% endif %}
              {% if project.github_backend %}
                <a class="project-code-link" href="{{ project.github_backend }}" target="_blank" rel="noopener noreferrer">
                  <i class="fa-brands fa-github"></i> Backend
                </a>
              {% endif %}
            {% elsif project.github %}
              <a class="project-code-link" href="{{ project.github }}" target="_blank" rel="noopener noreferrer">
                <i class="fa-brands fa-github"></i> Code
              </a>
            {% endif %}
            <p class="card-text">{{ project.description }}</p>
          </div>
        </div>
      </div>
    {% endfor %}
  </div>
  {% endif %}
{% endif %}
</div>
