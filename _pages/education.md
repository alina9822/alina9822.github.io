---
layout: page
permalink: /education/
title: education
description: Academic background and coursework.
nav: true
nav_order: 6
---

<!-- pages/education.md -->
<style>
  /* the gem's cv/education.liquid renders entries as <ul class="list-group ...">,
     but never resets list-style, so the browser's default bullet marker shows up
     beside each entry's date badge */
  .education-page ul.list-group {
    list-style: none;
    padding-left: 0;
    margin-left: 0;
  }

  .education-page.card {
    padding: 1.75rem 1.5rem;
    border: 1px solid var(--global-divider-color);
  }

  .education-page .list-group-item {
    padding: 1.25rem 0.5rem;
    border-bottom: 1px solid var(--global-divider-color);
  }

  .education-page .list-group-item:last-child {
    border-bottom: none;
    padding-bottom: 0.25rem;
  }

  .education-page .list-group-item:first-child {
    padding-top: 0.25rem;
  }

  .education-page .date-column {
    padding-left: 3rem;
  }

  .education-page .badge {
    background-color: var(--global-theme-color);
    color: var(--global-card-bg-color);
    font-size: 0.85rem;
    padding: 0.4rem 0.7rem;
    border-radius: 0.25rem;
  }

  .education-page .title a {
    color: var(--global-theme-color);
  }

  .education-page .courses-heading {
    color: var(--global-theme-color);
    text-transform: uppercase;
    letter-spacing: 0.04em;
    font-size: 0.8rem;
    font-weight: 700;
    margin-top: 1rem;
    margin-bottom: 0.5rem;
  }

  .education-page .items {
    padding-left: 1.1rem;
  }

  .education-page .items li {
    margin-bottom: 0.5rem;
  }

  .education-page .course-description {
    font-size: 0.82rem;
    color: var(--global-text-color-light);
    margin-top: 2px;
  }
</style>

<div class="card mt-3 p-3 education-page">
  <ul class="card-text font-weight-light list-group list-group-flush">
    {% assign entries = site.data.education.entries | al_cv_sort_by_date %}
    {% for entry in entries %}
      <li class="list-group-item">
        <div class="row">
          <div class="col-xs-2 col-sm-2 col-md-2 text-center date-column">
            {% capture start_date %}{% if entry.start_date %}{{ entry.start_date }}{% elsif entry.startDate %}{{ entry.startDate }}{% endif %}{% endcapture %}
            {% assign start_date = start_date | strip %}
            {% capture end_date %}{% if entry.end_date %}{{ entry.end_date }}{% elsif entry.endDate %}{{ entry.endDate }}{% endif %}{% endcapture %}
            {% assign end_date = end_date | strip %}
            {% capture point_date %}{% if entry.date %}{{ entry.date }}{% elsif entry.releaseDate %}{{ entry.releaseDate }}{% endif %}{% endcapture %}
            {% assign point_date = point_date | strip %}

            {% if start_date != '' %}
              {% assign startDate = start_date | split: '-' | first %}
              {% assign endDate = end_date | split: '-' | first | default: 'Present' %}
              {% assign date = startDate | append: ' - ' | append: endDate %}
            {% elsif point_date != '' %}
              {% assign date = point_date | split: '-' | first %}
            {% else %}
              {% assign date = null %}
            {% endif %}

            <table class="table-cv">
              <tbody>
                <tr>
                  <td>
                    {% if date %}
                      <span class="badge font-weight-bold danger-color-dark text-uppercase align-middle" style="min-width: 75px">{{ date }}</span>
                    {% endif %}
                  </td>
                </tr>
                {% capture location %}{% if entry.location %}{{ entry.location }}{% endif %}{% endcapture %}
                {% assign location = location | strip %}
                {% if location != '' %}
                  <tr>
                    <td>
                      <p class="location"><i class="fa-solid fa-location-dot iconlocation"></i> {{ location }}</p>
                    </td>
                  </tr>
                {% endif %}
              </tbody>
            </table>
          </div>
          <div class="col-xs-10 col-sm-10 col-md-10 mt-2 mt-md-0">
            {% capture study_type %}{% if entry.studyType %}{{ entry.studyType }}{% elsif entry.degree %}{{ entry.degree }}{% endif %}{% endcapture %}

            {% if entry.url %}
              <h6 class="title font-weight-bold ml-1 ml-md-4"><a href="{{ entry.url }}">{{ study_type }}</a></h6>
            {% elsif study_type != '' %}
              <h6 class="title font-weight-bold ml-1 ml-md-4">{{ study_type }}</h6>
            {% endif %}

            {% if entry.institution %}
              <h6 class="ml-1 ml-md-4" style="font-size: 0.95rem">{{ entry.institution }}</h6>
            {% endif %}

            {% if entry.area %}
              <h6 class="ml-1 ml-md-4" style="font-size: 0.95rem; font-style: italic">{{ entry.area }}</h6>
            {% endif %}

            {% if entry.highlights %}
              <ul class="items ml-1 ml-md-4">
                {% for item in entry.highlights %}
                  <li><span class="item">{{ item | markdownify | remove: '<p>' | remove: '</p>' }}</span></li>
                {% endfor %}
              </ul>
            {% endif %}

            {% if entry.courses %}
              <h6 class="courses-heading ml-1 ml-md-4">Notable Courses</h6>
              <ul class="items ml-1 ml-md-4">
                {% for course in entry.courses %}
                  <li>
                    <span class="item">{{ course.name }}</span>
                    {% if course.description %}
                      <div class="course-description">{{ course.description }}</div>
                    {% endif %}
                  </li>
                {% endfor %}
              </ul>
            {% endif %}
          </div>
        </div>
      </li>
    {% endfor %}
  </ul>
</div>
