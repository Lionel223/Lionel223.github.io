---
layout: page
title: 文章列表
sidebar_link: true
permalink: /archieve/
---

{% for post in site.posts %}
{% unless post.next %}
  <h3>{{ post.date | date: '%Y' }}</h3>
{% else %}
  {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
  {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
  {% if year != nyear %}
  <h3>{{ post.date | date: '%Y' }}</h3>
  {% endif %}
{% endunless %}

<ol>    
    <div class="month">
        <p class="archive-post-title">
            {{ post.date | date:"%b" }}
            <a href="{{ post.url }}">{{ post.title }}</a>
        </p>
    </div>
    <!-- <p class="archive-post-title"><a href="{{ post.url }}">{{ post.title }}</a></p> -->
</ol>
{% endfor %}