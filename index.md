---
layout: default
title: Epic Poetry Club
---
<div class="Home">
  <p class="Home-about">
    The Epic Poetry Club is a community dedicated to the appreciation of english language epic poetry.
  </p>

  <div class="Home-posts">
    <a href="jumble.social/nprofile1qqszrqlfgavys8g0zf8mmy79dn92ghn723wwawx49py0nqjn7jtmjagpz3mhxue69uhhyetvv9ujuerpd46hxtnfduqs6amnwvaz7tmwdaejumr0ds55yg6w" target="_blank" rel="noopener">
        <img
            src="https://nostr-wot.com/widgets/feed/npub1yxp7j36cfqws7yj0hkfu2mx25308u4zua6ud22zglxp98ayhh96s8c399s"
            alt="Super Testnet on Nostr"
            style="width: 100%; max-width: 600px;" 
        />
    </a>
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
