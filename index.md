---
layout: default
title: "Home"
nav: "yes"
sortTitle: "a"
---

<!-- HEADER -->
<div class="home">
	<div class="container">
		<div class="title">
			<h1>Fruits</h1>
			<p>An example Jekyll site built from a CSV datafile plus a couple of templates.</p>
		</div>
	</div>
</div>

<!-- TILES -->
<div class="container">
	<h2>Categories</h2>
	<div class="row tiles">
	<!-- 'map' so only category property + 'uniq' to remove duplicates => simple list of cats -->
	{% assign cats = site.data.fruits | map: "category"| uniq | sort  %}
	{% for category in cats %}
		{% include tile.html %}
	{% endfor %}
	</div>
</div>
