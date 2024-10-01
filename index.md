---
title: Ejercicios de Azure OpenAI
permalink: index.html
layout: home
---

# Desarrollo de aplicaciones de IA generativa con Inteligencia artificial de Azure Studio

Los ejercicios siguientes están diseñados para proporcionarte una experiencia práctica de aprendizaje en la que explorarás patrones y técnicas comunes que los desarrolladores usan para crear aplicaciones de IA generativas como "Copilots" basadas en chat y aprender a implementar esos patrones mediante Servicios de Azure AI, en particular, Azure OpenAI Service e Inteligencia artificial de Azure Studio.

Aunque puedes completar estos ejercicios por tu cuenta, están diseñados para complementar módulos en [Microsoft Learn](https://learn.microsoft.com/training/paths/create-custom-copilots-ai-studio/); en los que encontrarás un análisis más profundo de algunos de los conceptos subyacentes en los que se basan estos ejercicios.

> **Nota**: para completar los ejercicios, necesitarás una suscripción de Azure en la que tengas permisos y cuota suficientes para aprovisionar los recursos de Azure usados por Inteligencia artificial de Azure Studio e implementar y usar modelos de GPT de Azure OpenAI. Si aún no tienes una, puedes registrarte para obtener una [cuenta de Azure](https://azure.microsoft.com/free). Hay una opción de evaluación gratuita para los nuevos usuarios que incluye créditos durante los primeros 30 días.

## Ejercicios

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% para la actividad en laboratorios %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}