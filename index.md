---
layout: page
title: Hello World
tagline: Supporting tagline
---
{% include JB/setup %}



## What We See


		Ψ֮�c������ȥ�׺Σ���֮�c������ȥ���Σ���֮��η�����ɲ�η��
		���⣬��δ���գ���������������̫�Σ��紺��̨���Ҫ����⣬��δ�ף�
		�����⣬��냺֮δ���������⣬���o���w�����˽����N�����Ҫ����z��
		������֮��Ҳ�գ������⣡�������ѣ��Ҫ���衣���˲�죬�Ҫ�������
		���⣬�����������⣬���oֹ�����˽����ԣ����Ҫ��B�Ʊɡ��Ҫ����
		�ˣ����Fʳĸ��

The theme should reference these variables whenever needed.
    

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; 
	<a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
{% endfor %}



