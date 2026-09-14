---
layout: page
title: open source tools
permalink: /opensource/
description: Open-source tools I've built and released publicly.
nav: true
nav_order: 4
horizontal: false
---

<!-- pages/opensource.md -->
<div class="projects">
{% assign sorted_projects = site.projects | where: "category", "open-source" | sort: "importance" %}
  <div class="row row-cols-1 row-cols-md-3">
  {% for project in sorted_projects %}
    {% include projects.liquid %}
  {% endfor %}
  </div>
</div>
