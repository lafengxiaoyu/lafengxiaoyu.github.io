---
layout: page
permalink: /repositories/
title: repositories
description: My GitHub repositories and contributions
nav: true
nav_order: 4
---

## GitHub Profile

Visit my GitHub profile: [github.com/lafengxiaoyu](https://github.com/lafengxiaoyu)

---

## Featured Repositories

{% if site.data.repositories.github_repos %}
<div class="repositories">
  {% for repo in site.data.repositories.github_repos %}
    <div class="repo p-2">
      <a href="https://github.com/{{ repo }}" class="btn btn-outline-primary btn-block text-left">
        <i class="fab fa-github"></i> {{ repo }}
      </a>
    </div>
  {% endfor %}
</div>
{% endif %}
