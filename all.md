---
layout: default
title: "All fruits"
nav: "yes"
sortTitle: "c"
filter: "yes"
---

<div class="container" style="padding-top: 2rem">


<div class="container" style="padding-top: 2rem">

	<div class="row">
		<div class="col">
			<h1>All fruits</h1>
		</div>
		<div class="col-lg-3 col-md-3 col-sm-12">
			<form class="form-inline">
				 <input class="form-control" id="filterFruit" type="text" placeholder="Filter...">
			</form>
		</div>
	</div>

	<div class="row tiles">

{% for fruit in site.data.fruits %}

{% include card.html %}

{% endfor %}

	</div>
</div>
