---
layout: page
permalink: /publications/
title: Publications/Talks
description: 
years: [2023, 2022]
nav: true
nav_order: 2
---

<!-- _pages/publications.md -->

See [Google Scholar](https://scholar.google.com/citations?hl=en&user=UXNdqWgAAAAJ) for a complete list of publications.

<div class="publications">

{%- for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>

<hr>

## Invited Talks
- ICSA Applied Statistics Symposium, 2024, Nashville, USA
- ENAR Spring Meeting, 2024, Baltimore, USA
- CM Statistics, 2023, Berlin, Germany
- The 9th International Forum on Statistics, 2023, Beijing, China

