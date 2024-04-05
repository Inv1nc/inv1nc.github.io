---
layout: page
title: Search
permalink: search
---

<div id="search-container">
    <input type="text" id="search-input" placeholder="Search through the blog posts...">
    <ul id="results-container"></ul>
</div>

<section>
  {% if site.posts[0] %}

    {% capture currentyear %}{{ 'now' | date: "%Y" }}{% endcapture %}
    {% capture firstpostyear %}{{ site.posts[0].date | date: '%Y' }}{% endcapture %}
    {% if currentyear == firstpostyear %}
        <h3>This year's posts</h3>
    {% else %}  
        <h3>{{ firstpostyear }}</h3>
    {% endif %}

    {%for post in site.posts %}
      {% unless post.next %}
        <ul>
      {% else %}
        {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
        {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
        {% if year != nyear %}
          </ul>
          <h3>{{ post.date | date: '%Y' }}</h3>
          <ul>
        {% endif %}
      {% endunless %}
        <li><time>{{ post.date | date:"%d %b" }} - </time>
          <a href="{{ post.url | prepend: site.baseurl | replace: '//', '/' }}">
            {{ post.title }}
          </a>
        </li>
    {% endfor %}
    </ul>

  {% endif %}
</section>

<script src="{{ site.baseurl }}/assets/js/simple-jekyll-search.min.js" type="text/javascript"></script>

<script>
    SimpleJekyllSearch({
    searchInput: document.getElementById('search-input'),
    resultsContainer: document.getElementById('results-container'),
    searchResultTemplate: '<div style="text-align: left !important;"><a href="{url}"><h1 style="text-align:left !important;">{title}</h1></a><span style="text-align:left !important;">{date}</span></div>',
    json: '{{ site.baseurl }}/search.json'
    });
</script>
