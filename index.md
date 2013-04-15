---
layout: page
title: Hello World
tagline: Supporting tagline
---
{% include JB/setup %}



## What We See


		唯之與阿，相去幾何？美之與惡，相去若何？人之所畏，不可不畏。
		荒兮，其未央哉！眾人熙熙，如享太牢，如春登台。我獨泊兮，其未兆；
		沌沌兮，如嬰兒之未孩；儡儡兮，若無所歸。眾人皆有餘，而我獨若遺。
		我愚人之心也哉，沌沌兮！俗人昭昭，我獨昏昏。俗人察察，我獨悶悶。
		淡兮，其若海，望兮，若無止。眾人皆有以，而我獨頑似鄙。我獨異於
		人，而貴食母。

The theme should reference these variables whenever needed.
    
## Recently Posts

{% for post in site.posts %}
- ### [{{ post.title }}]({{ post.url }}) <time>{{ post.date | date: '%Y-%m-%d'}}</time>

  {{post.summary}}

  [Reading more]({{ post.url }})
{% endfor %}



