---
layout: default
title: Venato Verba
---
<div class="Home">
  <p class="Home-about">
      I am a poet who loves to write english language epic poetry.
  </p>

  <div class="Home-posts">
    Venato Verba's latest social media posts:<br><br>
    <!-- Place this container where you want the feed to appear -->
    <div 
        id="nostr-feed-widget" 
        data-npub="npub1ul2ng8gpwrn98saww7qx6qjmezp0w3s9aazuuumluyhvw0sxjzcsndx4tt"
        data-relays="wss://relay.damus.io,wss://nostr.mom"
    >
      <p>Loading latest Nostr posts...</p>
    </div>
    
    <!-- Nostr Widget Script -->
    <script type="module">
        // Uses nostr-tools via CDN for easy integration
        import { nip19, SimplePool } from 'https://cdn.jsdelivr.net/npm/nostr-tools@2.23.12/+esm';
    
        async function initNostrFeed() {
            var container = document.getElementById('nostr-feed-widget');
            if (!container) return;
    
            // Extract parameters
            var npub = container.getAttribute('data-npub');
            var defaultRelays = ["wss://relay.damus.io", "wss://nos.lol"];
            var relayAttr = container.getAttribute('data-relays');
            var relays = relayAttr ? relayAttr.split(',').map(r => r.trim()) : defaultRelays;
    
            if (!npub) {
                container.innerHTML = '<p>Error: No data-npub provided.</p>';
                return;
            }
    
            try {
                // Decode npub to hex public key
                var { data: pubkey } = nip19.decode(npub);
    
                // Initialize the Nostr connection pool
                var pool = new SimplePool();
    
                // Fetch the latest 3 text notes (Kind 1) from the user
                var posts = await pool.querySync(relays, {
                    authors: [pubkey],
                    kinds: [ 1 ],
                    limit: 3
                });
    
                // Sort posts by newest first
                posts.sort((a, b) => b.created_at - a.created_at);
    
                if (posts.length === 0) {
                    container.innerHTML = '<p>No recent posts found.</p>';
                    return;
                }
    
                // Build the HTML structure
                var html = '<div class="nostr-posts-list">';
          
                posts.forEach(post => {
                    var date = new Date(post.created_at * 1000).toLocaleDateString();
                    // Clean up content slightly (escapes basic HTML characters)
                    var safeContent = post.content
                    .replace(/&/g, "&amp;")
                    .replace(/</g, "&lt;")
                    .replace(/>/g, "&gt;")
                    .replace(/\n/g, "<br>");
                    var charlength = 100;
                    if ( safeContent.length > charlength ) {
                        safeContent = safeContent.substring( 0, charlength );
                        var i; for ( i=0; i<charlength; i++ ) {
                            if ( safeContent.substring( charlength - 1 ) === " " ) safeContent = safeContent.substring( 0, safeContent.length - 1 );
                        }
                        if ( safeContent.substring( charlength - 1 ) === "." ) safeContent = safeContent.substring( 0, safeContent.length - 1 );
                        safeContent = safeContent + "...";
                    }
    
                    html += `
                        <div class="nostr-post">
                            <small style="color: #666;">${date}</small>
                            <p style="margin: 5px 0;">${safeContent}</p>
                        </div>
                    `;
                });
    
                html += '</div>';
    
                // Append the Jumble profile link
                html += `
                    <div style="margin-top: 15px;">
                        <a href="https://jumble.social/users/${npub}" target="_blank" rel="noopener noreferrer" class="nostr-profile-link">
                            View full profile on jumble.social →
                        </a>
                    </div>
                `;
    
                container.innerHTML = html;
    
                // Clean up connections
                pool.close(relays);
    
            } catch (error) {
                console.error(error);
                container.innerHTML = '<p>Failed to load Nostr feed.</p>';
            }
        }
    
        // Run on page load
        document.addEventListener('DOMContentLoaded', initNostrFeed);
    </script>
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
