---
layout: page
title: Note
description: 记录点点滴滴
keywords: 学习笔记
comments: false
menu: 笔记
permalink: /note/
---

> 记录点点滴滴

<ul class="listing">
{% for note in site.note %}
{% if note.title != "Note Template" %}
<li class="listing-item"><a href="{{ note.url }}">{{ note.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
