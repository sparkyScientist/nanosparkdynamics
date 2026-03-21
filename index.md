---
layout: default
title: Home
description: The personal research portfolio of Jui Junnarkar.
---

<div class="hero-bg text-white">
  <div class="bg-black bg-opacity-50">
    <div class="max-w-4xl mx-auto px-6 py-32 text-center">
      <h1 class="text-6xl font-bold">NanoSpark Dynamics</h1>
      <p class="text-xl mt-4">Synthesizing Advanced Nanomaterials with Plasma and CFD</p>
    </div>
  </div>
</div>

<section class="max-w-4xl mx-auto px-6 py-12">
  <h2 class="text-4xl font-bold text-center mb-12">Recent Posts</h2>
  <div class="grid md:grid-cols-2 gap-12">
    {% for post in site.posts limit:2 %}
    <div class="bg-white rounded-2xl shadow-lg overflow-hidden border border-gray-200 flex flex-col">
      {% if post.image %}
  <img src="{{ post.image | relative_url }}" alt="{{ post.image_alt | default: post.title }}" class="w-full h-48 object-cover">
{% endif %}

      <div class="p-6 flex flex-col flex-grow">
        <h3 class="text-2xl font-bold mb-2"><a href="{{ post.url | relative_url }}" class="hover:underline">{{ post.title }}</a></h3>
        <p class="text-gray-600 mb-4 flex-grow">{{ post.summary | default: post.excerpt | strip_html | truncate: 140 }}</p>
        <a href="{{ post.url | relative_url }}" class="text-blue-600 font-semibold hover:underline self-start">Read More →</a>
      </div>
    </div>
    {% endfor %}
  </div>
</section>
