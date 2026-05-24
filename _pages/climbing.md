---
layout: page
title: climbing
permalink: /climbing/
nav: true
nav_order: 3

images:
  photoswipe: true

# To add a photo: drop the file in /assets/img/climbing/ and add an entry below.
# width/height are the *original* pixel dimensions (PhotoSwipe needs them to size the lightbox).
# Set `hero: true` to render that photo as a full-width row instead of a square grid tile.
photos:
  - src: climbing-2025-11-a.jpg
    width: 4000
    height: 1800
    position: 25% center
    hero: true
  - src: climbing-2025-11-b.jpg
    width: 4000
    height: 1800
    position: 25% center
  - src: climbing-2025-11-c.jpg
    width: 1201
    height: 1600
  - src: climbing-2025-08-18.jpg
    width: 3072
    height: 4096
  - src: climbing-2025-03-28.jpg
    width: 3472
    height: 4624
  - src: climbing-2025-02-13.jpg
    width: 3472
    height: 4624
  - src: climbing-2024-11-10.jpg
    width: 1600
    height: 1201
  - src: climbing-img-0662.jpg
    width: 4284
    height: 5712
  - src: climbing-fb-1.jpg
    width: 1440
    height: 1800
  - src: climbing-2023-04-01.jpg
    width: 3072
    height: 4096
  - src: climbing-2023-08-12.jpg
    width: 4000
    height: 1868
    hero: true
---

<style>
  #climbing-gallery .climbing-tile {
    display: block;
    width: 100%;
    aspect-ratio: 1 / 1;
    overflow: hidden;
    border-radius: 0.375rem;
  }
  #climbing-gallery .climbing-tile.climbing-hero {
    aspect-ratio: 20 / 9;
  }
  #climbing-gallery .climbing-tile img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    object-position: center;
    transition: transform 0.25s ease;
  }
  #climbing-gallery .climbing-tile:hover img {
    transform: scale(1.03);
  }
</style>

{% if page.photos and page.photos.size > 0 %}
<div class="pswp-gallery" id="climbing-gallery">
  {% assign in_grid = false %}
  {% for photo in page.photos %}
    {% assign src = '/assets/img/climbing/' | append: photo.src | relative_url %}
    {% if photo.hero %}
      {% if in_grid %}</div>{% assign in_grid = false %}{% endif %}
      <div class="mb-2">
        <a
          class="climbing-tile climbing-hero"
          href="{{ src }}"
          data-pswp-width="{{ photo.width }}"
          data-pswp-height="{{ photo.height }}"
          target="_blank"
          rel="noreferrer"
        >
          <img src="{{ src }}" alt="{{ photo.caption | default: 'climbing photo' }}" loading="{% if forloop.first %}eager{% else %}lazy{% endif %}"{% if photo.position %} style="object-position: {{ photo.position }};"{% endif %} />
        </a>
        {% if photo.caption %}
          <p class="text-center text-muted small mt-1 mb-0">{{ photo.caption }}</p>
        {% endif %}
      </div>
    {% else %}
      {% unless in_grid %}<div class="row row-cols-2 row-cols-sm-3 row-cols-md-3 g-2 mb-2">{% assign in_grid = true %}{% endunless %}
      <div class="col">
        <a
          class="climbing-tile"
          href="{{ src }}"
          data-pswp-width="{{ photo.width }}"
          data-pswp-height="{{ photo.height }}"
          target="_blank"
          rel="noreferrer"
        >
          <img src="{{ src }}" alt="{{ photo.caption | default: 'climbing photo' }}" loading="lazy"{% if photo.position %} style="object-position: {{ photo.position }};"{% endif %} />
        </a>
        {% if photo.caption %}
          <p class="text-center text-muted small mt-1 mb-0">{{ photo.caption }}</p>
        {% endif %}
      </div>
    {% endif %}
  {% endfor %}
  {% if in_grid %}</div>{% endif %}
</div>
{% else %}
<p><em>Photos coming soon.</em></p>
{% endif %}
