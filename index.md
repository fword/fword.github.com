---
layout: page
title: mceiba的个人博客
head_title: 晨旭园
description: |
  屌丝的技术生涯
---
{% include JB/setup %}



## Update Author Attributes


		唯之與阿，相去幾何？美之與惡，相去若何？人之所畏，不可不畏。
		荒兮，其未央哉！眾人熙熙，如享太牢，如春登台。我獨泊兮，其未兆；
		沌沌兮，如嬰兒之未孩；儡儡兮，若無所歸。眾人皆有餘，而我獨若遺。
		我愚人之心也哉，沌沌兮！俗人昭昭，我獨昏昏。俗人察察，我獨悶悶。
		淡兮，其若海，望兮，若無止。眾人皆有以，而我獨頑似鄙。我獨異於
		人，而貴食母。

The theme should reference these variables whenever needed.
    
## Sample Posts
Here's a sample "posts list".

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## To-Do

This theme is still unfinished. If you'd like to be added as a contributor, [please fork](http://github.com/plusjade/jekyll-bootstrap)!
We need to clean up the themes, make theme usage guides with theme-specific markup examples.


