---
layout: page
title: Note
description: 记录点点滴滴
keywords: 学习笔记
comments: true
menu: 笔记
permalink: /note/
---

> God made relatives. Thank God we can choose our friends.

<ul class="listing">
{% for note in site.note %}
{% if note.title != "Note Template" %}
<li class="listing-item"><a href="{{ note.url }}">{{ note.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
