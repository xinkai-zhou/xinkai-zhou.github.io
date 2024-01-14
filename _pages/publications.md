---
layout: page
permalink: /publications/
title: Publications
description: 
years: [2023, 2022]
nav: true
nav_order: 2
---

<!-- _pages/publications.md -->
<div class="publications">

{%- for y in page.years %}
  <h2 class="year">{{y}}</h2>
  {% bibliography -f papers -q @*[year={{y}}]* %}
{% endfor %}

</div>


See [Google Scholar](https://scholar.google.com/citations?hl=en&user=UXNdqWgAAAAJ) for a complete list of publications.