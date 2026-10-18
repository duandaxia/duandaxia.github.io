---
layout: page
title: blog
permalink: /blog/
description: Notes on robot learning, research, and the ideas behind the work.
nav: true
nav_order: 3
---

{% assign personal_posts = site.posts | where: 'personal_blog', true %}

<ul class="post-list list-unstyled">
  {% for post in personal_posts %}
    <li class="mb-5">
      <p class="post-meta"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: '%B %d, %Y' }}</time> &middot; {{ post.meta }}</p>
      <h2><a class="post-title" href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
      <p>{{ post.description }}</p>
      <a href="{{ post.url | relative_url }}">Read the essay &rarr;</a>
    </li>
  {% endfor %}
</ul>
