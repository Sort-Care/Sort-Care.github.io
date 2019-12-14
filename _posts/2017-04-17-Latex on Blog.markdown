---
layout: single
title:  "Add Latex Support for Minimal Mistakes"
date:   2018-11-21 09:08:55 +0800
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
Check [MathJax Documentation][Mathjax-doc] for this step's instructions. Note that you might need ``config=TeX-MML-AM_CHTML`` at the end of your src value separated by a question mark in the following form to enable predefined configurations. Add the following into your website, it will load the latest MathJax library.
{% highlight html %}
<script type="text/javascript" async
	src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/latest.js?config=TeX-MML-AM_CHTML">
</script>
{% endhighlight %}
And usually, if you are using a jekyll theme, this include line should go to a file in the ``_includes/`` folder. In my case, I put it into the ``scripts.html`` file. You can also create a file for doing this, a file named ``custom.html`` for an example.

### Adding configurations to ``scripts.html``
(Thanks to **Abhijit S.**, I forgot to put this part into this blog.)  
We have included the MathJax library, and next we need to add some configurations and make it actually work pretty on our website. The goal is that we want
the website to be able to recognize all normal Latex symbols for math equations and symbols: ``$$`` and ``\[\]``. To do that, add the following
code into ``scripts.html``:
{% highlight html %}
<script type="text/x-mathjax-config">
   MathJax.Hub.Config({
     extensions: ["tex2jax.js"],
     jax: ["input/TeX", "output/HTML-CSS"],
     tex2jax: {
       inlineMath: [ ['$','$'], ["\\(","\\)"] ],
       displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
       processEscapes: true
     },
     "HTML-CSS": { availableFonts: ["TeX"] }
   });
</script>
{% endhighlight %}
The meaning of this code is rather straight forward. You can add any symbols you want to enrich the ways MathJax pick up math equations.

### Putting Math Equations on Your Webpage
If everything goes fine, you should get a working site with Mathjax. For usages of writing mathematics on page, check [MathJax Doc][Mathjax-page]. Use my blog as an example:  
Math code:
{% highlight latex %}
\[p(\theta) = \mathbf{\prod}_{i,c}p(\mathbf{\theta}^i(c))\]
{% endhighlight %}
or
{% highlight latex %}
$$ E = mc^2 $$
{% endhighlight %}

Will be rendered to:

\[ p(\theta) = \mathbf{\prod}_{i,c}p(\mathbf{\theta}^i(c)) \]

and 

$$ E = mc^2 $$

Note that, in my case, it seems like you better **leave blank lines** in your markdown file **around a Latex Code Block** to make it be centered after generated.

## Using Katex instead of Mathjax
Katex is faster than Mathjax. To migrate, first check [kramdown doc][kramdown-katex]. You need ``JQuery``, ``Katex`` and an automatic rendering script. Check [Katex on Github][katex-git] and [Auto render][auto-render]. After doing these, you should have a working site with Katex.


[jekyll-doc]:https://jekyllrb.com/docs/configuration/
[Mathjax-doc]:http://docs.mathjax.org/en/latest/web/start.html
[Mathjax-page]:http://docs.mathjax.org/en/latest/start.html#putting-mathematics-in-a-web-page
[kramdown-katex]:https://kramdown.gettalong.org/math_engine/mathjax.html
[katex-git]:https://github.com/Khan/KaTeX
[auto-render]:https://github.com/Khan/KaTeX/blob/master/contrib/auto-render/README.md

