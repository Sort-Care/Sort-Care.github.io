---
layout: single
title: "Emacs Error: Invalid function: org-babel-header-args-safe-fn"
date: 2017-06-05 23:13 +0800
mathjax: false

excerpt: "An error caused by org or org-plus-contrib package."
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.4
---
{% include toc title="Contents" %}

## Possible Cause
This problem is highly possible to be caused by this settings in init files:
{% highlight emacs-lisp %}
;;auto load languages
(org-babel-do-load-languages
 'org-babel-load-languages
 '(
   (sh . t)
   (python . t)
   (R . t)
   (ruby . t)
   (ditaa . t)
   (dot . t)
   (octave . t)
   (sqlite . t)
   (perl . t)
   (C . t)
   ))
{% endhighlight %}

There are many people who encountered with exactly the same problem because of <span style="color: #f0f0f0; background-color: #6495ED; padding:0.06em 0.5em 0.06em;border-radius: 6px;box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);">org</span> and <span style="color: #f0f0f0; background-color: #1E90FF; padding:0.06em 0.5em 0.06em;border-radius: 6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);">org-plus-contrib</span>. So I thought this could be the root cause. But shortly it was found that I didn't have <span style="color: #f0f0f0; background-color: #1E90FF; padding:0.06em 0.5em 0.06em;border-radius: 6px;">org-plus-contrib</span> at all. So the real cause remained unclear. I would appreciate it if you are willing to share what you find about this error in comments.

## Solution
You can simply comment this configuration out to get rid of the launch error. The final solution is provided in [this email][email-link]. 

The upshot was run 
<span style="color: #f0f0f0; background-color: #FF69B4; padding:0.06em 0.5em 0.06em;border-radius: 6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);">
``M-x``</span>
***byte-compile-file***  on file 
<span style="color: #f0f0f0; background-color: #7B68EE; padding:0.06em 0.5em 0.06em;border-radius: 6px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);">
ob-R.el.
</span>

If you don't know where the file ``ob-R.el`` is, just search in your emacs configuration folder. After compiled, if everything goes fine, the error should have go away. 

However, some may encounter new errors while compiling. Refer to [this email][error-link] for a temporary fix. In case the link could be expired, I copied the email contents here:
> Unfortunately I now can't reproduce the problem, so I can't check if my suggestion to byte compile ob-R.el works or not. When I did observe the issue you describe that seemed to fix it, and at least one other person confirmed that it worked for them as well.
>
> Since it doesn't work for you I suggest removing org-plus-contrib (or org), starting emacs with ``emacs -Q`` and running something like
{% highlight emacs-lisp %}
(require 'package)
(add-to-list 'package-archives
             '("org" . "http://orgmode.org/elpa/";) t)
(package-initialize)
(package-install 'org-plus-contrib)
;; or (package-install 'org)
{% endhighlight %}
> Best,
>
> Ista


[email-link]:https://lists.gnu.org/archive/html/emacs-orgmode/2015-08/msg00280.html
[error-link]:https://lists.gnu.org/archive/html/emacs-orgmode/2015-08/msg00316.html
