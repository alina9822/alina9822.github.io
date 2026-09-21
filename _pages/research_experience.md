---
layout: page
permalink: /research-experience/
title: research experience
description: A collection of research experiences with detailed timelines and resources.
nav: true
nav_order: 3
---

<!-- pages/research_experience.md -->
<style>
  /* make each research-experience tile span the full row instead of the
     gem's default auto-fill card grid (minmax(300px, 1fr)) */
  .research-experience .course-list {
    grid-template-columns: 1fr;
  }

  /* disabled for now until each entry has a real page to link to;
     remove this rule to re-enable the link */
  .research-experience .course-title a {
    pointer-events: none;
    cursor: default;
  }

  .research-experience .course-content {
    margin-top: 0.75rem;
  }

  .research-experience .course-content img {
    max-width: 100%;
    height: auto;
    margin: 0.5rem 0;
  }

  .research-experience .course-content h2 {
    font-size: 1.15rem;
    margin-top: 1rem;
  }
</style>
{% if site.research_experiences %}
  <div class="courses research-experience">
    {% assign experiences_by_year = site.research_experiences | sort: 'year' | reverse | group_by: 'year' %}

    {% for year_group in experiences_by_year %}
      <h2 class="year">{{ year_group.name }}</h2>
      <div class="course-list">
        {% assign year_experiences = year_group.items | sort: 'term' %}
        {% for experience in year_experiences %}
          <div class="course-item">
            <h3 class="course-title">
              <a href="{{ experience.url | relative_url }}">{{ experience.title }}</a>
            </h3>

            <div class="course-meta">
              {% if experience.term %}
                <span class="course-term">{{ experience.term }}</span>
              {% endif %}

              {% if experience.instructor %}
                <span class="course-instructor">{{ experience.instructor }}</span>
              {% endif %}
            </div>

            {% if experience.description %}
              <div class="course-description">
                {{ experience.description | markdownify }}
              </div>
            {% endif %}

            <div class="course-content">
              {{ experience.content }}
            </div>
          </div>
        {% endfor %}
      </div>
    {% endfor %}
  </div>
{% else %}
  <p>No research experiences available yet.</p>
{% endif %}
