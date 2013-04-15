---
layout: page
title: 道沖而用之或不盈
tagline: Supporting tagline
---
{% include JB/setup %}


  
<img src="/assets/images/p.jpg" width="700" height="432">



<embed src="http://www.xiami.com/widget/0_1320787/singlePlayer.swf" type="application/x-shockwave-flash" width="257" height="33" wmode="transparent"></embed>

## Recently Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo;
	<a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>




