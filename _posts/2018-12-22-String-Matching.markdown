---
layout: single
title: String Matching
date: 2018-12-22 10:47 +0800
mathjax: true

excerpt: "String matching problem algorithms."
header:
  teaser: /assets/images/economics.jpg
  overlay_image: /assets/images/economics.jpg
  overlay_filter: 0.5
---

This post is my note I kept when I was reading the Introduction to Algorithms book.

{% include toc title="Contents" %}
<span style = "display:block;
	float:left;
	font-family:Georgia;
	font-size: 310%;
	font-weight: bold;
	line-height: 90%;
	margin-right: 6px;
	margin-bottom:-2px;
	margin-top: 7px;">S</span>tring matching problem is formalized as follows.  
1. $T[1\cdots n]$ : An array of length $n$.
2. $P[1\cdots m]$ : An array of the pattern that we are looking for.
3. Assumption: Both elements of $P$ and $T$ are characters drawn from a finite alphabet $\Sigma$.

According to problems, $\Sigma$ could be $\\{0, 1\\}$ or $\\{a, b, \ldots, z \\}$.

And, to make describing the problem easier, we define the following concepts:
1. **$P$ occurs with shift s** (starting from 0): Or pattern $P$ occurs beginning at position $s+1$(starting from 1) if
$0 \leq s \leq n-m $ and $T[s+1\ldots s+m] = P[1\ldots m]$. And such a shift position is also called a **valid shift**.
Otherwise, a shift is an **invalid shift**.

Thus, the string matching is the problem of finding all valid shifts with which a given pattern $P$ occurs in a given text $T$.

# Overview of String Matching Algorithms

| Algorithm              | Preprocessing Time       | Matching Time |
|------------------------|--------------------------|---------------|
| Naive                  | 0                        | $O((n-m+1)m)$ |
| Rabin-Karp             | $\Theta(m)$              | $O((n-m+1)m)$ |
| Finite Automaton       | $O(m\vert \Sigma \vert)$ | $\Theta(n)$   |
| **Knuth-Morris-Pratt** | $\Theta(m)$              | $\Theta(n)$   |

The first time I saw this table, I was confused completely. The Rabin-Karp method is definitely off somewhere. Its total running time
is worse than the Naive method. Yes, that's true for the worst cases. But the Rabin-Karp algorithm works much better on average and
in practice. And it generalizes nicely to other pattern-matching problems. 

# Notation and Terminology
- $\Sigma ^ \ast$ : the set of all finite-length strings formed using characters from the alphabet $\Sigma$
- **Concatenation**: the concatenation of two strings $x$ and $y$ is denoted $xy$.
- **Prefix**: String $w$ is a prefix of a string $x$, denoted $w \sqsubset x$, if $x = wy$ for some string $y \in \Sigma^\ast$.
- **Suffix**: denoted $w \sqsupset x$, if $x = yw$ for some $y \in \Sigma^\ast$.
- **Empty String**: denoted $\varepsilon$

By definition, empty string is both a suffix and a prefix of all strings.

From the notations we already have, we can move forward to have the following lemma.
- **Overlapping-suffix Lemma** : Suppose that $x$, $y$, and $z$ are strings such that $x\sqsupset z$ and $y \sqsupset z$.
If $|x| \leq |z|$, then $x \sqsupset y$. If $|x|\geq |y|$, then $y\sqsupset x$. If $|x| = |y|$, then x=y.
