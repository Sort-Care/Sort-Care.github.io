---
layout: single
title: Optimization Tips
date: 2019-02-22 10:47 +0800
mathjax: false

excerpt: "Optimization Tips (mainly for C++)"

header:
  teaser: /assets/images/circle.png
  overlay_image: /assets/images/circle.png
  overlay_filter: 0.4
---
{% include toc title="Contents" %}

I am learning about optimization these days. Most of the content came from *TAOCP*, *Comptetitive Programming* and the internet.
From TAOCP, I learned that understanding what the machine is doing in machine code is crucial for optimize things. For example, 
I didn't realize how much time we can save by replace `i++` with `i+=2` until I went through the sequential search chapter. I did 
an experiment to validate if this is still true nowadays. It is, and I will include that experiment in another post.
For this post, I am going to note down all quick tips I found useful for optimizing code.

# Optimization Overview


# Optimization Tips
1. The C++ STL provided `algorithm::sort` is cache-friendly. While the `heapsort` is not (its root-to-leaf/leaf-to-root operations
span a wide range of indices-lots of cache misses).
2. Access a 2D array in a row major fashion.
3. Bit manipulation(bitmask) on the built-in integer data types(64 bits max) is more efficient than index manipulation in an array of booleans.
If more than 64 bits are needed, use STL `bitset`.
4. Prefer lower level DS/types at all times if possible.
5. Avoid passing big data structures around.
6. Always prefer an iterative approach to a recursive approach.
7. Array access in (nested) loops can be slow. Use a temporary instead of accessing it repeatedly.
8. Wisely take advantage of macros and inline functions.
9. Use C-style character arrays will yield faster execution than using STL `string`.


