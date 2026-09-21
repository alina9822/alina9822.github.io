---
layout: page
permalink: /work-experience/
title: Work Experience
description: Professional experience and roles.
nav: true
nav_order: 4
---

<!-- pages/work_experience.md -->
<style>
  /* same list-style reset as the education page: the gem's list-group markup
     never sets list-style:none, so the browser's default bullet shows through */
  .work-experience-page ul.list-group {
    list-style: none;
    padding-left: 0;
    margin-left: 0;
  }

  .work-experience-page.card {
    padding: 1.75rem 1.5rem;
    border: 1px solid var(--global-divider-color);
  }

  .work-experience-page .list-group-item {
    padding: 1.25rem 0.5rem;
    border-bottom: 1px solid var(--global-divider-color);
  }

  .work-experience-page .list-group-item:last-child {
    border-bottom: none;
    padding-bottom: 0.25rem;
  }

  .work-experience-page .list-group-item:first-child {
    padding-top: 0.25rem;
  }

  .work-experience-page .date-column {
    padding-left: 1.5rem;
  }

  .work-experience-page .badge {
    background-color: var(--global-theme-color);
    color: var(--global-card-bg-color);
    font-size: 0.85rem;
    padding: 0.4rem 0.7rem;
    border-radius: 0.25rem;
  }

  .work-experience-page .title a {
    color: var(--global-theme-color);
  }

  .work-experience-page .summary {
    font-size: 0.9rem;
    margin-top: 0.5rem;
  }

  .work-experience-page .highlights-heading {
    color: var(--global-theme-color);
    text-transform: uppercase;
    letter-spacing: 0.04em;
    font-size: 0.8rem;
    font-weight: 700;
    margin-top: 1rem;
    margin-bottom: 0.5rem;
  }

  .work-experience-page .items {
    padding-left: 1.1rem;
  }

  .work-experience-page .items li {
    margin-bottom: 0.5rem;
  }

  .work-experience-section-heading {
    font-size: 1.2rem;
    margin-top: 2rem;
    margin-bottom: 0;
  }

  .work-experience-section-heading:first-of-type {
    margin-top: 0;
  }

  /* de-emphasize the secondary section so the professional role stays the focus */
  .work-experience-page.other-experience {
    padding: 1.25rem 1.5rem;
  }

  .work-experience-page.other-experience .title {
    font-size: 0.95rem;
  }

  .work-experience-page.other-experience .badge {
    background-color: transparent;
    color: var(--global-text-color-light);
    border: 1px solid var(--global-divider-color);
  }
</style>

{% assign professional_entries = site.data.work_experience.entries | where: "type", "professional" | al_cv_sort_by_date %}
{% if professional_entries.size > 0 %}
  <h2 class="work-experience-section-heading">Professional Experience</h2>
  <div class="card mt-3 p-3 work-experience-page">
    <ul class="card-text font-weight-light list-group list-group-flush">
      {% for entry in professional_entries %}
        <li class="list-group-item">
          <div class="row">
            <div class="col-xs-2 col-sm-2 col-md-2 text-center date-column">
              {% capture start_date %}{% if entry.start_date %}{{ entry.start_date }}{% endif %}{% endcapture %}
              {% assign start_date = start_date | strip %}
              {% capture end_date %}{% if entry.end_date %}{{ entry.end_date }}{% endif %}{% endcapture %}
              {% assign end_date = end_date | strip %}

              {% if start_date != '' %}
                {% assign startDate = start_date | split: '-' | first %}
                {% assign endDate = end_date | split: '-' | first | default: 'Present' %}
                {% assign date = startDate | append: ' - ' | append: endDate %}
              {% else %}
                {% assign date = null %}
              {% endif %}

              <table class="table-cv">
                <tbody>
                  <tr>
                    <td>
                      {% if date %}
                        <span class="badge font-weight-bold text-uppercase align-middle" style="min-width: 75px">{{ date }}</span>
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
              {% if entry.url %}
                <h6 class="title font-weight-bold ml-1 ml-md-4"><a href="{{ entry.url }}">{{ entry.position }}</a></h6>
              {% elsif entry.position %}
                <h6 class="title font-weight-bold ml-1 ml-md-4">{{ entry.position }}</h6>
              {% endif %}

              {% if entry.company %}
                <h6 class="ml-1 ml-md-4" style="font-size: 0.95rem">{{ entry.company }}</h6>
              {% endif %}

              {% if entry.summary %}
                <p class="summary ml-1 ml-md-4">{{ entry.summary }}</p>
              {% endif %}

              {% if entry.highlights %}
                <h6 class="highlights-heading ml-1 ml-md-4">Contributions</h6>
                <ul class="items ml-1 ml-md-4">
                  {% for item in entry.highlights %}
                    <li><span class="item">{{ item | markdownify | remove: '<p>' | remove: '</p>' }}</span></li>
                  {% endfor %}
                </ul>
              {% endif %}
            </div>
          </div>
        </li>
      {% endfor %}
    </ul>
  </div>
{% endif %}

{% assign other_entries = site.data.work_experience.entries | where: "type", "other" | al_cv_sort_by_date %}
{% if other_entries.size > 0 %}
  <h2 class="work-experience-section-heading">Other Experience</h2>
  <div class="card mt-3 p-3 work-experience-page other-experience">
    <ul class="card-text font-weight-light list-group list-group-flush">
      {% for entry in other_entries %}
        <li class="list-group-item">
          <div class="row">
            <div class="col-xs-2 col-sm-2 col-md-2 text-center date-column">
              {% capture start_date %}{% if entry.start_date %}{{ entry.start_date }}{% endif %}{% endcapture %}
              {% assign start_date = start_date | strip %}
              {% capture end_date %}{% if entry.end_date %}{{ entry.end_date }}{% endif %}{% endcapture %}
              {% assign end_date = end_date | strip %}

              {% if start_date != '' %}
                {% assign startDate = start_date | split: '-' | first %}
                {% assign endDate = end_date | split: '-' | first | default: 'Present' %}
                {% assign date = startDate | append: ' - ' | append: endDate %}
              {% else %}
                {% assign date = null %}
              {% endif %}

              <table class="table-cv">
                <tbody>
                  <tr>
                    <td>
                      {% if date %}
                        <span class="badge font-weight-bold text-uppercase align-middle" style="min-width: 75px">{{ date }}</span>
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
              {% if entry.url %}
                <h6 class="title font-weight-bold ml-1 ml-md-4"><a href="{{ entry.url }}">{{ entry.position }}</a></h6>
              {% elsif entry.position %}
                <h6 class="title font-weight-bold ml-1 ml-md-4">{{ entry.position }}</h6>
              {% endif %}

              {% if entry.company %}
                <h6 class="ml-1 ml-md-4" style="font-size: 0.95rem">{{ entry.company }}</h6>
              {% endif %}

              {% if entry.summary %}
                <p class="summary ml-1 ml-md-4">{{ entry.summary }}</p>
              {% endif %}

              {% if entry.highlights %}
                <ul class="items ml-1 ml-md-4">
                  {% for item in entry.highlights %}
                    <li><span class="item">{{ item | markdownify | remove: '<p>' | remove: '</p>' }}</span></li>
                  {% endfor %}
                </ul>
              {% endif %}
            </div>
          </div>
        </li>
      {% endfor %}
    </ul>
  </div>
{% endif %}
