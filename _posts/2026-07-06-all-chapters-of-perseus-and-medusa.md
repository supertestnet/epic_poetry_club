---
layout: post
type: standard_post
title: "All chapters of Perseus and Medusa"
---

Last week I finished reciting the first drafts of my book, "Perseus and Medusa." Listen to my recitals at this playlist:

<!-- Custom Video Player Layout -->
<div class="custom-player-wrapper">
  
  <!-- 1. The Main Video Player Frame -->
  <div class="main-video-container">
    <iframe id="video-frame" src="" frameborder="0" allowfullscreen></iframe>
  </div>

  <!-- 2. The Interactive Video List Below -->
  <div class="playlist-items-list" id="playlist-menu">
    <!-- Items are generated dynamically via JavaScript below -->
  </div>

</div>

<!-- Styles Specific to the Playlist Setup -->
<style>
  .custom-player-wrapper {
    max-width: 560px; /* Forces exactly the same standard width as standard embeds */
    margin: 2rem auto;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  }

  /* Main video wrapper establishing 16:9 responsive aspect ratio */
  .main-video-container {
    position: relative;
    padding-bottom: 56.25%; /* 16:9 Aspect Ratio */
    height: 0;
    overflow: hidden;
    background-color: #000;
    border-radius: 8px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.15);
  }

  .main-video-container iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }

  /* List container styling */
  .playlist-items-list {
    margin-top: 15px;
    display: flex;
    flex-direction: column;
    gap: 8px;
    max-height: 400px; /* Optional: adds scrolling if you have tons of videos */
    overflow-y: auto;
  }

  /* Individual row styling */
  .playlist-row-item {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 8px;
    background-color: #f7f9fa;
    border: 1px solid #e1e4e8;
    border-radius: 6px;
    cursor: pointer;
    transition: all 0.2s ease;
  }

  .playlist-row-item:hover {
    background-color: #f1f3f5;
    border-color: #0366d6;
  }

  .playlist-row-item.active-playing {
    background-color: #e8f0fe;
    border-color: #1a73e8;
    font-weight: bold;
  }

  /* Thumbnail dimension normalization */
  .playlist-thumb-box {
    position: relative;
    width: 80px;
    height: 45px; /* Perfect 16:9 aspect scale down */
    flex-shrink: 0;
  }

  .playlist-thumb-box img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    border-radius: 4px;
  }

  /* Text layouting inside the row */
  .playlist-metadata {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
    min-width: 0; /* Prevents long titles from overflowing text boundaries */
  }

  .playlist-video-title {
    font-size: 14px;
    color: #24292e;
    margin: 0 0 2px 0;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }

  .playlist-video-duration {
    font-size: 12px;
    color: #586069;
    margin: 0;
  }
</style>

<!-- Player Operations JavaScript Engine -->
<script>
  // UPDATE THIS ARRAY WITH YOUR OWN YOUTUBE VIDEO DATA
  const videoPlaylist = [
    { id: "D9upj9AGqUU", title: "Perseus and Medusa Chapter 01", length: "8:04" },
    { id: "Yl0y_WuMRKA", title: "Perseus and Medusa Chapter 02", length: "7:25" },
    { id: "uXpM_r64pAk", title: "Perseus and Medusa Chapter 03", length: "8:34" },
    { id: "rz0_-ZJjS_0", title: "Perseus and Medusa Chapter 04", length: "8:31" },
    { id: "mk1PimknBsw", title: "Perseus and Medusa Chapter 05", length: "9:25" },
    { id: "NuCk2dUlPY8", title: "Perseus and Medusa Chapter 06", length: "8:46" },
    { id: "-mihIVJ8_3Y", title: "Perseus and Medusa Chapter 07", length: "10:25" },
    { id: "az7K9qO4UWY", title: "Perseus and Medusa Chapter 08", length: "7:47" },
    { id: "nntWJJnwd5E", title: "Perseus and Medusa Chapter 09", length: "8:39" },
    { id: "wByK1wiwE-4", title: "Perseus and Medusa Chapter 10", length: "10:09" },
    { id: "hIfhxuw2E5w", title: "Perseus and Medusa Chapter 11", length: "10:01" },
    { id: "ERAsv8uhTZc", title: "Perseus and Medusa Chapter 12", length: "8:25" },
    { id: "-WV8NLfmUKg", title: "Perseus and Medusa Chapter 13", length: "8:50" },
    { id: "b6y_N4DUUxE", title: "Perseus and Medusa Chapter 14", length: "9:45" },
    { id: "K2FRPrGi20k", title: "Perseus and Medusa Chapter 15", length: "9:37" },
  ];

  const iframeEl = document.getElementById('video-frame');
  const menuEl = document.getElementById('playlist-menu');

  // Pre-loads the primary video in the array on structural load
  function initCustomPlayer() {
    if (videoPlaylist.length === 0) return;
    
    // Set initial frame target source
    changeMainVideo(videoPlaylist[0].id, false);

    // Build item directory elements dynamically
    videoPlaylist.forEach((video, index) => {
      const itemRow = document.createElement('div');
      itemRow.className = `playlist-row-item ${index === 0 ? 'active-playing' : ''}`;
      itemRow.setAttribute('data-id', video.id);
      
      // Pulling clean static thumbnails straight from YouTube standard content delivery servers
      itemRow.innerHTML = `
        <div class="playlist-thumb-box">
          <img src="https://img.youtube.com/vi/${video.id}/mqdefault.jpg" alt="${video.title}">
        </div>
        <div class="playlist-metadata">
          <p class="playlist-video-title">${video.title}</p>
          <p class="playlist-video-duration">${video.length}</p>
        </div>
      `;

      // Assign mouse trigger mechanism
      itemRow.addEventListener('click', () => {
        // Toggle physical visual highlight configurations across list rows
        document.querySelectorAll('.playlist-row-item').forEach(el => el.classList.remove('active-playing'));
        itemRow.classList.add('active-playing');
        
        // Command iframe content target conversion
        changeMainVideo(video.id, false);
      });

      menuEl.appendChild(itemRow);
    });
  }

  function changeMainVideo(videoId, shouldAutoplay) {
    // Appends internal system flag modifiers for seamless page operations
    let embedUrl = `https://www.youtube.com/embed/${videoId}`;
    if (shouldAutoplay) {
      embedUrl += "&autoplay=1";
    }
    iframeEl.src = embedUrl;
  }

  // Engage structural script initializing hooks safely
  document.addEventListener("DOMContentLoaded", initCustomPlayer);
</script>
