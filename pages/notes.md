---
layout: page
title: Notes
description: 
keywords: 
comments: false
menu: 笔记
permalink: /notes/
---

<ul class="listing">
{% for note in site.notes %}
{% if note.title != "Note Template" %}
<li class="listing-item"><a href="{{ note.url }}">{{ note.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
