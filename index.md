---
layout: default
title: Epic Poetry Club
---
<div class="Home">
  <p class="Home-about">
    The Epic Poetry Club is a community dedicated to the appreciation of english language epic poetry.
  </p>

  <div class="Home-posts">
    <a class="twitter-timeline" data-height="600" href="https://x.com/VenatoVerba?ref_src=twsrc%5Etfw">Posts by VenatoVerba</a> 
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>
    <h2 class="Home-posts-title">Recent posts on this website</h2>
    {% assign event_posts = 7 %}
    {% assign counter = 0 %}
    {% for post in site.posts %}
      {% if post.type == "standard_post"%}
        <div class="Home-posts-post">
          <span class="Home-posts-post-date">{{ post.date | date_to_string }}</span>
          <span class="Home-posts-post-arrow">&raquo;</span>
          <a class="Home-posts-post-title" href="{{ post.url }}">{{ post.title }}</a>
        </div>
        {% assign counter = counter | plus: 1 %}
        {% if counter == event_posts %}
          {% break %} {% comment %}exit the for loop{% endcomment %}
        {% endif %}
      {% endif %}
    {% endfor %}
    <p style="display: flex; justify-content: space-between;"><a href="/posts.html">See all posts</a> <a href="/feed.xml">See rss feed</a></p>
  </div>
</div>
