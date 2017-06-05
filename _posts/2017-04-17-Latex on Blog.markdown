---
layout: single
title:  "Add Latex Support for Minimal Mistakes"
date:   2017-04-17 09:08:55 +0800
#categories: iOS
mathjax: true

excerpt: "How to make your blog support Latex"
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.4 # same as adding an opacity of 0.5 to a black background
  
---

{% include toc title="Contents" %}

## Add Latex Support for Minimal Mistakes

If you are seeing the latex name below, it means latex works for this site:

$$ \LaTeX $$

## Mathjax
### Set Markdown Engine to Kramdown
Check [jekyll documentations][jekyll-doc] for this step's instructions.

### Include Scripts
Check [Mathjax fdocumentations][Mathjax-doc] for this step's instructions. Note that you might need ``config=TeX-MML-AM_CHTML`` at the end of your src value separated by a question mark in the following form to enable predefined configurations:
{% highlight html %}
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}
And usually, if you are using a jekyll theme, this include line should go to a file in the ``_includes/`` folder. In my case, I put it into the ``scripts.html`` file. You can also create a file for doing this, a file named ``custom.html`` for an example.

### Putting mathematics in a web page
If everything goes fine, you should get a working site with Mathjax. For usages of writing mathematics on page, check [MathJax Doc][Mathjax-page]. Use my blog as an example:  
Math code:
{% highlight latex %}
\[p(\theta) = \mathbf{\prod}_{i,c}p(\mathbf{\theta}^i(c))\]
{% endhighlight %}

Will be rendered to:

$$ p(\theta) = \mathbf{\prod}_{i,c}p(\mathbf{\theta}^i(c))$$

## Using Katex instead of Mathjax
Katex is faster than Mathjax. To migrate, first check [kramdown doc][kramdown-katex]. You need ``JQuery``, ``Katex`` and an automatic rendering script. Check [Katex on Github][katex-git] and [Auto render][auto-render]. After doing these, you should have a working site with Katex.



[jekyll-doc]:https://jekyllrb.com/docs/configuration/
[Mathjax-doc]:http://docs.mathjax.org/en/latest/start.html
[Mathjax-page]:http://docs.mathjax.org/en/latest/start.html#putting-mathematics-in-a-web-page
[kramdown-katex]:https://kramdown.gettalong.org/math_engine/mathjax.html
[katex-git]:https://github.com/Khan/KaTeX
[auto-render]:https://github.com/Khan/KaTeX/blob/master/contrib/auto-render/README.md

