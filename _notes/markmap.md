---
layout: note
title: Markmap
categories: Markmap
description: Markmap
keywords: Markmap
---

-----

**目录**

* TOC
{:toc}

## 语法

```markdown

# markmap

## Links

- <https://markmap.js.org/>
- [GitHub](https://github.com/gera2ld/markmap)

## Related

- [coc-markmap](https://github.com/gera2ld/coc-markmap)
- [gatsby-remark-markmap](https://github.com/gera2ld/gatsby-remark-markmap)

## Features

- links
- **strong** ~~del~~ *italic* ==highlight==

-----

- multiline
  text
- `inline code`
-
    ```js
    console.log('code block');
    ```
- Katex - $x = {-b \pm \sqrt{b^2-4ac} \over 2a}$
- Now we can wrap very very very very long text based on `maxWidth` option

```

## 效果

<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta http-equiv="X-UA-Compatible" content="ie=edge">
<title>Markmap</title>
<style>
* {
  margin: 0;
  padding: 0;
}
#mindmap {
  display: block;
  width: 100vw;
  height: 100vh;
}
</style>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/prismjs@1.25.0/themes/prism.css"><link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.12.0/dist/katex.min.css">
</head>
<body>
<svg id="mindmap"></svg>
<script src="https://cdn.jsdelivr.net/npm/d3@6.7.0"></script><script src="https://cdn.jsdelivr.net/npm/markmap-view@0.13.2"></script><script>(r=>{window.WebFontConfig={custom:{families:["KaTeX_AMS","KaTeX_Caligraphic:n4,n7","KaTeX_Fraktur:n4,n7","KaTeX_Main:n4,n7,i4,i7","KaTeX_Math:i4,i7","KaTeX_Script","KaTeX_SansSerif:n4,n7,i4","KaTeX_Size1","KaTeX_Size2","KaTeX_Size3","KaTeX_Size4","KaTeX_Typewriter"]},active:()=>{r().refreshHook.call()}}})(()=>window.markmap)</script><script src="https://cdn.jsdelivr.net/npm/webfontloader@1.6.28/webfontloader.js" defer></script><script>((w,x,k,M)=>{const _=w();window.mm=_.Markmap.create("svg#mindmap",x==null?void 0:x(_,M),k)})(()=>window.markmap,(e,t)=>e.deriveOptions(t),{"type":"heading","depth":1,"payload":{"lines":[0,1]},"content":"markmap","children":[{"type":"heading","depth":2,"payload":{"lines":[2,3]},"content":"Links","children":[{"type":"list_item","depth":4,"payload":{"lines":[4,5]},"content":"<a href=\"https://markmap.js.org/\">https://markmap.js.org/</a>"},{"type":"list_item","depth":4,"payload":{"lines":[5,6]},"content":"<a href=\"https://github.com/gera2ld/markmap\">GitHub</a>"}]},{"type":"heading","depth":2,"payload":{"lines":[7,8]},"content":"Related","children":[{"type":"list_item","depth":4,"payload":{"lines":[9,10]},"content":"<a href=\"https://github.com/gera2ld/coc-markmap\">coc-markmap</a>"},{"type":"list_item","depth":4,"payload":{"lines":[10,11]},"content":"<a href=\"https://github.com/gera2ld/gatsby-remark-markmap\">gatsby-remark-markmap</a>"}]},{"type":"heading","depth":2,"payload":{"lines":[12,13]},"content":"Features","children":[{"type":"bullet_list","depth":3,"payload":{"lines":[14,17]},"content":"","children":[{"type":"list_item","depth":4,"payload":{"lines":[14,15]},"content":"links"},{"type":"list_item","depth":4,"payload":{"lines":[15,16]},"content":"<strong>strong</strong> <del>del</del> <em>italic</em> <mark>highlight</mark>"}]},{"type":"bullet_list","depth":3,"payload":{"lines":[19,28]},"content":"","children":[{"type":"list_item","depth":4,"payload":{"lines":[19,21]},"content":"multiline<br>\ntext"},{"type":"list_item","depth":4,"payload":{"lines":[21,22]},"content":"<code>inline code</code>"},{"type":"list_item","depth":4,"payload":{"lines":[22,26]},"content":"<pre class=\"language-js\"><code class=\"language-js\">console<span class=\"token punctuation\">.</span><span class=\"token function\">log</span><span class=\"token punctuation\">(</span><span class=\"token string\">'code block'</span><span class=\"token punctuation\">)</span><span class=\"token punctuation\">;</span>\n</code></pre>\n"},{"type":"list_item","depth":4,"payload":{"lines":[26,27]},"content":"Katex - <span class=\"katex\"><span class=\"katex-mathml\"><math xmlns=\"http://www.w3.org/1998/Math/MathML\"><semantics><mrow><mi>x</mi><mo>=</mo><mfrac><mrow><mo>−</mo><mi>b</mi><mo>±</mo><msqrt><mrow><msup><mi>b</mi><mn>2</mn></msup><mo>−</mo><mn>4</mn><mi>a</mi><mi>c</mi></mrow></msqrt></mrow><mrow><mn>2</mn><mi>a</mi></mrow></mfrac></mrow><annotation encoding=\"application/x-tex\">x = {-b \\pm \\sqrt{b^2-4ac} \\over 2a}</annotation></semantics></math></span><span class=\"katex-html\" aria-hidden=\"true\"><span class=\"base\"><span class=\"strut\" style=\"height:0.4306em;\"></span><span class=\"mord mathnormal\">x</span><span class=\"mspace\" style=\"margin-right:0.2778em;\"></span><span class=\"mrel\">=</span><span class=\"mspace\" style=\"margin-right:0.2778em;\"></span></span><span class=\"base\"><span class=\"strut\" style=\"height:1.3845em;vertical-align:-0.345em;\"></span><span class=\"mord\"><span class=\"mord\"><span class=\"mopen nulldelimiter\"></span><span class=\"mfrac\"><span class=\"vlist-t vlist-t2\"><span class=\"vlist-r\"><span class=\"vlist\" style=\"height:1.0395em;\"><span style=\"top:-2.655em;\"><span class=\"pstrut\" style=\"height:3em;\"></span><span class=\"sizing reset-size6 size3 mtight\"><span class=\"mord mtight\"><span class=\"mord mtight\">2</span><span class=\"mord mathnormal mtight\">a</span></span></span></span><span style=\"top:-3.23em;\"><span class=\"pstrut\" style=\"height:3em;\"></span><span class=\"frac-line\" style=\"border-bottom-width:0.04em;\"></span></span><span style=\"top:-3.394em;\"><span class=\"pstrut\" style=\"height:3em;\"></span><span class=\"sizing reset-size6 size3 mtight\"><span class=\"mord mtight\"><span class=\"mord mtight\">−</span><span class=\"mord mathnormal mtight\">b</span><span class=\"mbin mtight\">±</span><span class=\"mord sqrt mtight\"><span class=\"vlist-t vlist-t2\"><span class=\"vlist-r\"><span class=\"vlist\" style=\"height:0.9221em;\"><span class=\"svg-align\" style=\"top:-3em;\"><span class=\"pstrut\" style=\"height:3em;\"></span><span class=\"mord mtight\" style=\"padding-left:0.833em;\"><span class=\"mord mtight\"><span class=\"mord mathnormal mtight\">b</span><span class=\"msupsub\"><span class=\"vlist-t\"><span class=\"vlist-r\"><span class=\"vlist\" style=\"height:0.7463em;\"><span style=\"top:-2.786em;margin-right:0.0714em;\"><span class=\"pstrut\" style=\"height:2.5em;\"></span><span class=\"sizing reset-size3 size1 mtight\"><span class=\"mord mtight\">2</span></span></span></span></span></span></span></span><span class=\"mbin mtight\">−</span><span class=\"mord mtight\">4</span><span class=\"mord mathnormal mtight\">a</span><span class=\"mord mathnormal mtight\">c</span></span></span><span style=\"top:-2.8821em;\"><span class=\"pstrut\" style=\"height:3em;\"></span><span class=\"hide-tail mtight\" style=\"min-width:0.853em;height:1.08em;\"><svg xmlns=\"http://www.w3.org/2000/svg\" width='400em' height='1.08em' viewBox='0 0 400000 1080' preserveAspectRatio='xMinYMin slice'><path d='M95,702\nc-2.7,0,-7.17,-2.7,-13.5,-8c-5.8,-5.3,-9.5,-10,-9.5,-14\nc0,-2,0.3,-3.3,1,-4c1.3,-2.7,23.83,-20.7,67.5,-54\nc44.2,-33.3,65.8,-50.3,66.5,-51c1.3,-1.3,3,-2,5,-2c4.7,0,8.7,3.3,12,10\ns173,378,173,378c0.7,0,35.3,-71,104,-213c68.7,-142,137.5,-285,206.5,-429\nc69,-144,104.5,-217.7,106.5,-221\nl0 -0\nc5.3,-9.3,12,-14,20,-14\nH400000v40H845.2724\ns-225.272,467,-225.272,467s-235,486,-235,486c-2.7,4.7,-9,7,-19,7\nc-6,0,-10,-1,-12,-3s-194,-422,-194,-422s-65,47,-65,47z\nM834 80h400000v40h-400000z'/></svg></span></span></span><span class=\"vlist-s\">​</span></span><span class=\"vlist-r\"><span class=\"vlist\" style=\"height:0.1179em;\"><span></span></span></span></span></span></span></span></span></span><span class=\"vlist-s\">​</span></span><span class=\"vlist-r\"><span class=\"vlist\" style=\"height:0.345em;\"><span></span></span></span></span></span><span class=\"mclose nulldelimiter\"></span></span></span></span></span></span>"},{"type":"list_item","depth":4,"payload":{"lines":[27,28]},"content":"Now we can wrap very very very very long text based on <code>maxWidth</code> option"}]}]}]},null)</script>
</body>
</html>

## 试试

[markmap try it out](https://markmap.js.org/repl)