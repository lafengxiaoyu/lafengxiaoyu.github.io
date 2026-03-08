---
layout: page
permalink: /repositories/
title: repositories
description: My GitHub repositories and contributions
nav: true
nav_order: 4
---

## GitHub Profile

[![GitHub Stats](https://github-readme-stats.vercel.app/api?username=lafengxiaoyu&theme=default&show_icons=true)](https://github.com/lafengxiaoyu)

---

## Featured Repositories

{% if site.data.repositories.github_repos %}
<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
  {% for repo in site.data.repositories.github_repos %}
    {% include repository/repo.html repository=repo %}
  {% endfor %}
</div>
{% endif %}
