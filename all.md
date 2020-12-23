---
layout: default
title: "All fruits"
nav: "yes"
sortTitle: "c"
---

<div class="container" style="padding-top: 2rem">


<div class="container" style="padding-top: 2rem">
	<h1>All fruits</h1>
	<div class="row">

{% for fruit in site.data.fruits %}

{% include card.html %}

{% endfor %}

	</div>
</div>
