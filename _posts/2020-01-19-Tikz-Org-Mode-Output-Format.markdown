---
layout: single
title: Tikzpicture Unable to Genrating Png in Org
date: 2020-01-19 10:47 +0800
mathjax: false

excerpt: "Fixing generating png files with tikzpicture package in org mode."
header:
  teaser: /assets/images/economics.jpg
  overlay_image: /assets/images/economics.jpg
  overlay_filter: 0.5
---
{% include toc title="Contents" %}
<span style = "display:block;
	float:left;
	font-family:Georgia;
	font-size: 310%;
	font-weight: bold;
	line-height: 90%;
	margin-right: 6px;
	margin-bottom:-2px;
	margin-top: 7px;">I</span> was trying to embed `tikzpicture` code in my org files. But fount that whenever I want to output the latex source code written to generate a picture, I was only able to do it when the `:file` option is set to `svg`. It will give not authorized error for `png` files.

For example, I have the following code blocks:
```latex
#+name: 2.24-list-tree
#+header: :results value file
#+header: :file images/2.24-tree.png 
#+header: :imagemagick yes :headers '("\\usepackage{tikz}") 
#+header: :fit yes :iminoptions -density 600 :imoutoptions -geometry 400
#+begin_src latex :file ./images/2.24-tree.png
  \begin{tikzpicture}
    \node {root}
      child {node {1}}
      child {node {(2 (3 4))}
        child {node {2}}
        child {node {(3 4)}
          child {node {3}}
          child {node {4}}
        }
      };
  \end{tikzpicture}
#+end_src
```

and it never generates the file. Later I found that the issue is with the debian version of the package ~imagemagick~. In order to re-enable us to generate `png` files, do the following:
1. Switch to root user
2. Edit file `/etc/ImageMagic-6/policy.xml`
3. Replace `<policy domain="coder" rights="none" pattern="PDF" />` with  
<policy domain="coder" rights="read|write" pattern="PDF" />
4. Save it.

Now we should be able to generate `png` files just fine.

I found the solution [here](https://emacs-china.org/t/org-tikz/8891/19 "Draw pictures in Org Mode with Tikz").
