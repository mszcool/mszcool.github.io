---
layout: page
title: mszcool's thoughts and cents revealed
---

Welcome to my GitHub-pages. I decided to move my writing activities fully to GitHub to try something new and keep content as close as possible to actual code. Since I am doing this transition as a spare-time activity, it will take a while until I am done.

Cheers

**Mario**

{% for post in site.categories[page.category] %}
    <a href="{{ post.url | absolute_url }}">
      {{ post.title }}
    </a>
{% endfor %}