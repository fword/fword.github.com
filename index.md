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
    
## Recently Posts

{% for post in site.posts %}
- ### [{{ post.title }}]({{ post.url }}) <time>{{ post.date | date: '%Y-%m-%d'}}</time>

  {{post.summary}}

  [Reading more]({{ post.url }})
{% endfor %}



