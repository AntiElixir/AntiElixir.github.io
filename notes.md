---
title: Notes
permalink: /notes/
eyebrow: Notes
subtitle: "Reading notes, research logs, and things I find interesting."
description: "Notes by Mingliang Hao (郝铭梁) on AI, embodied intelligence, quantitative finance, basketball, and more."
---

<section class="section page-section">
  <div class="container">
    {% assign published_notes = site.notes | sort: "date" | reverse %}
    {% if published_notes.size > 0 %}
      <div class="note-card-grid">
        {% for note in published_notes %}
          <a class="note-card" href="{{ note.url | relative_url }}">
            <div>
              <p class="note-card-tags">{{ note.tags | join: " / " }}</p>
              <h3>{{ note.title }}</h3>
              <p>{{ note.excerpt | strip_html | truncate: 120 }}</p>
            </div>
          </a>
        {% endfor %}
      </div>
    {% else %}
      <div class="empty-state">
        <h2>The notebook is open, but the ink is still drying.</h2>
        <p>Research logs, reading notes, and interesting thoughts will live here.</p>
      </div>
    {% endif %}
  </div>
</section>
