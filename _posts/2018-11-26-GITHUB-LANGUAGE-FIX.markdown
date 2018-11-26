---
layout: single
title: Fix Wrong Github Repository Language
date: 2018-11-26 10:47 +0800
mathjax: false

excerpt: "What to do if github marks your repo to a wrong language"
header:
  teaser: /assets/images/circle.png
  overlay_image: /assets/images/circle.png
  overlay_filter: 0.5
---
{% include toc title="Contents" %}
## Solution: Change Linguist Vendor Attribute
<span style = "display:block;
	float:left;
	font-family:Georgia;
	font-size: 310%;
	font-weight: bold;
	line-height: 90%;
	margin-right: 6px;
	margin-bottom:-2px;
	margin-top: 7px;">G</span>ithub sometimes marks one repo's language wrong because of Linguist Library behavior. Here is a quick fix.

1. First, create file `.gitattributes` in the root folder of the repository you want to fix.
2. Write following lines into this file, and stage, commit, push to github.
```
* linguist-vendored
*.cpp linguist-vendored=false
*.hpp linguist-vendored=false
```

After doing this, the Linguist now only takes `.cpp` and `.hpp` files into consideration for deciding your repo's language. Change the lines according to which language you want it to recognize.

