---
title: Ejercicios de Azure OpenAI
permalink: index.html
layout: home
---

# Ejercicios de Inteligencia artificial de Azure Studio

Los siguientes ejercicios están diseñados como apoyo para los módulos de [Microsoft Learn](https://learn.microsoft.com/training).

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% para la actividad en laboratorios %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}