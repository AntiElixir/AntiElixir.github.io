---
title: Projects
permalink: /projects/
eyebrow: Projects
subtitle: "Research in ocean science, AI, and quantitative finance."
description: "Research projects by Hao Mingliang (郝铭梁) on ocean dynamics, magnetics tactile sensing, robotics learning, and quantitative finance."
---

<section class="section page-section">
  <div class="container">
    <div class="project-grid all-projects">
      {% assign sorted_works = site.works | sort: "order" %}
      {% for work in sorted_works %}
        {% unless work.hidden %}
          <a class="project-card project-card-{{ work.slug }}" href="{{ work.url | relative_url }}">
            {% if work.image %}{% include project-card-image.html src=work.image alt=work.title %}{% elsif work.status == "In progress" %}<div class="project-placeholder" role="img" aria-label="{{ work.title }} image coming soon">?</div>{% endif %}
            <div class="card-content">
              <p>{{ work.kind }}{% if work.status == "In progress" %}<span class="project-status">In progress</span>{% endif %}</p>
              <h3>{{ work.title }}</h3>
              <span>{{ work.subtitle }}</span>
            </div>
          </a>
        {% endunless %}
      {% endfor %}
    </div>
  </div>
</section>
