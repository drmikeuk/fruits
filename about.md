---
layout: page
title: "About"
nav: "yes"
sortTitle: "z"
---

How this prototype is built...

## Data

All items are listed in one file **fruits.csv** which is loaded into **site.data.fruits** array.

|fruitname|origin|category|colour|image|blurb|
|---------|------|--------|------|-----|-----|
|Bananas|Asia|tropical|yellow|Bananas.jpg|"Generally, it is agreed that bananas ..."|
Strawberries|North America|berries|red|Strawberries.jpg|"The garden strawberry ..."|
{:.table .table-sm .tableauto}


## All fruits index

Simply loop through all the items in the data file & display as cards:
{% highlight javascript%}
{% raw %}{%{% endraw %} for fruit in site.data.fruits {% raw %}%}{% endraw %}
  {% raw %}{%{% endraw %} include card.html {% raw %}%}{% endraw %}
{% raw %}{%{% endraw %} endfor {% raw %}%}{% endraw %}
{% endhighlight %}

Where card.html is as below.<br/>This calculates the link for each item from the **fruitname**; this matches the logic used by the jekyll-datapage_gen plugin when creating the pages.

{% highlight javascript%}
<div>
  {% raw %}{%{% endraw %} assign link = fruit.fruitname | remove: " " | downcase | prepend: "/fruits/"| append: ".html" {% raw %}%}{% endraw %}

  title:        {% raw %}{{{% endraw %} fruit.fruitname {% raw %}}}{% endraw %}
  thumbnail:    {% raw %}{{{% endraw %} fruit.image {% raw %}}}{% endraw %}
  descriptiuon: {% raw %}{{{% endraw %} fruit.blurb {% raw %}}}{% endraw %}
</div>
{% endhighlight %}



## Item  pages

Use the [jekyll-datapage_gen](https://github.com/avillafiorita/jekyll-datapage_gen) plugin to generate one page per **item** (row) in the datafile.

{% highlight javascript %}
plugins:
  - jekyll-datapage-generator

page_gen:
  - data: 'fruits'                                // datasource
    template: 'fruit'                             // page template
    dir: 'fruits'                                 // output directory
    name_expr: 'record["fruitname"].delete(" ")'  // create filename from this field (+ strip spaces)
    title: 'fruitname'                            // field for page title
{% endhighlight %}  

Creates page per item in /fruits/ eg [Bananas](/fruits/bananas.html).

#### Fruit template
All item data from the CSV is available as page variables. The variables are mapped to the CSV column headings eg

{% highlight html %}
<h1> {% raw %}{{{% endraw %} page.fruitname {% raw %}}}{% endraw %} </h1>
origin: {% raw %}{{{% endraw %} page.origin {% raw %}}}{% endraw %}
category: {% raw %}{{{% endraw %} page.category {% raw %}}}{% endraw %}
colour: {% raw %}{{{% endraw %} page.colour {% raw %}}}{% endraw %}
{% endhighlight %}

Can calculate the link for each **category** (eg "apples" links to the category page for "apples") from the category name; this matches the logic used by the jekyll-datapage_gen plugin when creating the category indexes (see below).

{% highlight javascript%}
{% raw %}{%{% endraw %} assign link = page.category | remove: " " | prepend: "/category/" | append: ".html" {% raw %}%}{% endraw %}
{% endhighlight %}



## Category index pages

Use the [jekyll-datapage_gen](https://github.com/avillafiorita/jekyll-datapage_gen) plugin to generate one page per **category** in the datafile. This category page can be used as an index for all items in this category.

I suspect it creates a page for each **item** but all items in one category have the same **filename** so end up with just one file per category.

{% highlight javascript %}
page_gen:
  - data: 'fruits'                                // datasource
    template: 'cat'                               // page template
    dir: 'category'                               // output directory
    name_expr: 'record["category"].delete(" ")'   // create filename from this field (+ strip spaces)
    title: 'category'                             // field for page title
{% endhighlight %}  


#### Cat template

All item data from the CSV is available as page variables. The variables are mapped to the CSV column headings. This includes **this category** eg

{% highlight html %}
<h1> {% raw %}{{{% endraw %} page.category | capitalize {% raw %}}}{% endraw %} </h1>
{% endhighlight %}  

Like the **All fruits index** loop  through all the items in the data file & display as cards. But first **filter** so just display ones in this category:

{% highlight javascript%}
{% raw %}{%{% endraw %} assign fruits = (site.data.fruits | where: "category", page.category) {% raw %}%}{% endraw %}
{% raw %}{%{% endraw %} for fruit in fruits {% raw %}%}{% endraw %}
  <div>
    title:        {% raw %}{{{% endraw %} fruit.fruitname {% raw %}}}{% endraw %}
    thumbnail:    {% raw %}{{{% endraw %} fruit.image {% raw %}}}{% endraw %}
    descriptiuon: {% raw %}{{{% endraw %} fruit.blurb {% raw %}}}{% endraw %}
  </div>
{% raw %}{%{% endraw %} endfor {% raw %}%}{% endraw %}
{% endhighlight %}

Creates page per category in /category/ eg [Tropical](/category/tropical.html)



## List of categories

Extract just the field of interest (eg category or colour) from all the items,
filter this to remove duplicates and then loop through this to produce a list of categories.

eg Categories: Apples, Berries, Citrus, ...<br/>
eg Colours: Black, Green, Orange, ...

{% highlight javascript%}
<ul>
<!-- 'map' so only category property + 'uniq' to remove duplicates => simple list of cats -->
{% raw %}{%{% endraw %} assign cats = site.data.fruits | map: "category"| uniq | sort {% raw %}%}{% endraw %}
{% raw %}{%{% endraw %} for cat in cats {% raw %}%}{% endraw %}
  <!-- remove spaces + top & tail => /category/<thiscat>.html -->
  {% raw %}{%{% endraw %} assign link = cat | remove: " " | prepend: "/category/" | append: ".html" {% raw %}%}{% endraw %}
  <li><a href="{% raw %}{{{% endraw %}link{% raw %}}}{% endraw %}">{% raw %}{{{% endraw %}cat | capitalize {% raw %}}}{% endraw %}</a></li>
{% raw %}{%{% endraw %} endfor {% raw %}%}{% endraw %}
</ul>
{% endhighlight %}


#### Tiles of categories

Similarly can create tiles, not just a list, of categories.

The key here is to have a category thumbnail, named after the category.
eg `/categoryThumbs/ <category>.jpg`
<br/> eg /categoryThumbs/apples.jpg

{% highlight javascript%}
<!-- 'map' so only category property + 'uniq' to remove duplicates => simple list of cats -->
{% raw %}{%{% endraw %} assign cats = site.data.fruits | map: "category"| uniq | sort  {% raw %}%}{% endraw %}
{% raw %}{%{% endraw %} for category in cats {% raw %}%}{% endraw %}
  {% raw %}{%{% endraw %} include tile.html {% raw %}%}{% endraw %}
{% raw %}{%{% endraw %} endfor {% raw %}%}{% endraw %}
{% endhighlight %}

Where tile.html is:

{% highlight javascript%}
<div class="tile">
  {% raw %}{%{% endraw %} assign thumb = category | remove: " " | downcase | prepend: "/categoryThumbs/"| append: ".jpg" {% raw %}%}{% endraw %}
  img:   {% raw %}{{{% endraw %} thumb {% raw %}}}{% endraw %}
  title: {% raw %}{{{% endraw %}category | capitalize {% raw %}}}{% endraw %}
  {% raw %}{%{% endraw %} assign link = category | remove: " " | downcase | prepend: "/category/"| append: ".html" {% raw %}%}{% endraw %}
</div>
{% endhighlight %}


..
