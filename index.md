---
layout: default
title: Home
---
<div class="Home">
  <p class="Home-about">
    BitDevs is a community for those interested in discussing and participating in the research and development of Bitcoin and related protocols.
  </p>
  <p class="Home-about">
    To donate: <a href="https://btcpay.sanjuanbitdevs.org/apps/32bsssoMC5JpcCvMKgfVXfmXUoy3/pos" target="_blank">click here</a>
  </p>

  <div class="Home-posts">
    <h2 class="Home-posts-title">Upcoming and Recent Events</h2>
    {% assign event_posts = 7 %}
    {% assign counter = 0 %}
    {% for post in site.posts %}
      {% if post.type == "socratic" or post.type == "whitepaper"%}
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
    <p><a href="/events.html">See all events</a></p>
  </div>
</div>

<div class="Home-posts">
  <h2 class="Home-posts-title">Special thanks</h2>
  <img src="https://sanjuanbitdevs.org/assets/pics/2026-06-30-second-gate.png" style="width: 100%; max-width: 300px; vertical-align: middle;"> <img src="https://sanjuanbitdevs.org/assets/pics/2026-06-30-nakamoto.png" style="width: 100%; max-width: 300px; vertical-align: middle;"> <img src="https://sanjuanbitdevs.org/assets/pics/2026-06-30-velas-commerce.png" style="width: 100%; max-width: 300px; vertical-align: middle;"> <img src="https://sanjuanbitdevs.org/assets/pics/2026-06-30-bitdevs-sj-logo.png" style="width: 100%; max-width: 300px; vertical-align: middle;">
</div>
