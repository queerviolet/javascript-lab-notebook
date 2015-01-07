---
title: The Javascript Lab Notebook
permalink: /
layout: specimen
next: /001_short_circuit_evaluation/
---

{% include_relative README.md %}

## Specimens ##

{% for f in site.pages %}
{% if f.category == 'specimen' %}
<a href="{{ site.baseurl }}{{ f.url }}">{{ f.title }}</a>
{% endif %}
{% endfor %}

## Colophon ##

{% include_relative COLOPHON.md %}

Site generated from branch gh-pages at {{ site.time }}.

## License ##

{% include_relative LICENSE.md %}
