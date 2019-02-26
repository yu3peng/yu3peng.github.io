---
layout: page
title: Wiki
description: 记录点点滴滴
keywords: 维基, Wiki
comments: false
menu: 维基
permalink: /wiki/
---

> 好记性不如个烂笔头，何况这个还不错

<ul class="listing">
{% for wiki in site.wiki %}
{% if wiki.title != "Wiki Template" %}
<li class="listing-item"><a href="{{ wiki.url }}">{{ wiki.title }}</a></li>
{% endif %}
{% endfor %}
</ul>
