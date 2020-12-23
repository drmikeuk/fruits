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
  <div>
    title:        {% raw %}{{{% endraw %} fruit.fruitname {% raw %}}}{% endraw %}
    thumbnail:    {% raw %}{{{% endraw %} fruit.image {% raw %}}}{% endraw %}
    descriptiuon: {% raw %}{{{% endraw %} fruit.blurb {% raw %}}}{% endraw %}
  </div>
{% raw %}{%{% endraw %} endfor {% raw %}%}{% endraw %}
{% endhighlight %}

Calculate the link for each item from the **fruitname**; this matches the logic used by the jekyll-datapage_gen plugin when creating the pages.

{% highlight javascript%}
{% raw %}{%{% endraw %} assign link = fruit.fruitname | remove: " " | downcase | prepend: "/fruits/"| append: ".html" {% raw %}%}{% endraw %}
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

### Fruit template
All item data (matches column headings from CSV) is available as page variables eg

{% highlight html %}
<h1> {% raw %}{{{% endraw %} page.fruitname {% raw %}}}{% endraw %} </h1>
origin: {% raw %}{{{% endraw %} page.origin {% raw %}}}{% endraw %}
category: {% raw %}{{{% endraw %} page.category {% raw %}}}{% endraw %}
colour: {% raw %}{{{% endraw %} page.colour {% raw %}}}{% endraw %}
{% endhighlight %}


## Category index

Use the [jekyll-datapage_gen](https://github.com/avillafiorita/jekyll-datapage_gen) plugin to generate one page per **category** in the datafile.
I suspect creates a page for each **item** but all items in one category have the same **filename** so end up with just one file per category.

{% highlight javascript %}
page_gen:
  - data: 'fruits'                                // datasource
    template: 'cat'                               // page template
    dir: 'category'                               // output directory
    name_expr: 'record["category"].delete(" ")'   // create filename from this field (+ strip spaces)
    title: 'category'                             // field for page title
{% endhighlight %}  


### Cat template

All item data (matches column headings from CSV) is available as page variables; this includes this category eg

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
