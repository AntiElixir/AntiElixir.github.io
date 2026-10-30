---
layout: default
title: Home
description: "Mingliang Hao (郝铭梁) is an undergraduate student at Shanghai Jiao Tong University. Research interests include embodied intelligence, robotics learning, and quantitative finance."
---

{% assign settings = site.data.settings %}

<section class="hero">
  <div class="container">
    <div class="hero-inner">
      <div class="hero-image">
        {% capture hero_alt %}{{ settings.author.name }}'s picture{% endcapture %}
        {% include responsive-photo.html src=settings.hero.image alt=hero_alt sizes="(max-width: 900px) 100vw, 42vw" fetchpriority="high" %}
      </div>
      <div class="hero-copy">
        <h1 class="hero-tagline">
          <span class="tagline-line"><span class="tagline-letter">A</span> <span class="tagline-letter">A</span>lways</span>
          <span class="tagline-line"><span class="tagline-letter">B</span> <span class="tagline-letter">B</span>e</span>
          <span class="tagline-line"><span class="tagline-letter">C</span> <span class="tagline-letter">C</span>urious</span>
          <span class="tagline-bottom">ALWAYS BE CURIOUS</span>
        </h1>
        {% if settings.hero.description %}
          <p>{{ settings.hero.description }}</p>
        {% endif %}
      </div>
    </div>
  </div>
</section>

<section class="section research-intro" aria-labelledby="about-me">
  <div class="container">
    <p class="eyebrow">About me</p>
    <h2 id="about-me">I build intelligent systems.</h2>
    <p class="research-summary">I'm an undergraduate at Shanghai Jiao Tong University. My work spans embodied intelligence, robotics learning, and quantitative finance.</p>
    <div class="research-directions">
      <div>
        <h3>Embodied intelligence & AI</h3>
        <p>How can magnetics tactile sensing improve robot learning and embodied intelligence?</p>
      </div>
      <div>
        <h3>Quantitative finance</h3>
        <p>How can machine learning models predict commodity futures markets?</p>
      </div>
    </div>
  </div>
</section>

<section class="section">
  <div class="container">
    <div class="section-head">
      <div>
        <h2>{{ settings.projects.title }}</h2>
        {% if settings.projects.description %}<p>{{ settings.projects.description }}</p>{% endif %}
      </div>
      <a href="{{ '/projects/' | relative_url }}">See all</a>
    </div>

    <div class="project-grid">
      {% assign sorted_works = site.works | sort: "order" %}
      {% for work in sorted_works limit:4 %}
        {% unless work.hidden %}
          <a class="project-card project-card-{{ work.slug }}" href="{{ work.url | relative_url }}">
            {% if work.image %}{% include project-card-image.html src=work.image alt=work.title %}{% endif %}
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

<section class="section" aria-labelledby="hobbies">
  <div class="container">
    <p class="eyebrow">Beyond research</p>
    <h2 id="hobbies">Hobbies</h2>
    <div class="hobbies-grid">
      <div class="hobby-card">
        <h3>Basketball</h3>
        <p>Small forward. I'm a shooter who loves practicing alone — just me, the ball, and the hoop. Those solo sessions teach me focus and patience.</p>
      </div>
      <div class="hobby-card">
        <h3>Phigros</h3>
        <p>Rhythm game enthusiast. Phigros's dynamic judgment lines and creative chart designs keep me coming back. Hitting notes to intense beats helps me unwind — currently pushing for rks 15.</p>
      </div>
      <div class="hobby-card">
        <h3>Reading</h3>
        <p>Wide-ranging — philosophy, psychology, sci-fi, finance, probability &amp; statistics, computer science, and more. I love how good books rewire the way I think about problems.</p>
      </div>
    </div>
  </div>
</section>

