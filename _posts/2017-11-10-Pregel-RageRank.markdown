---
layout: single
title:  "Pregel PageRank: A Simple Java Implementation"
date:   2017-11-10 09:08:55 +0800
#categories: iOS
mathjax: true

excerpt: "A Java program simulating Pregel PageRank Algorithm with threads and message queue"
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.4 # same as adding an opacity of 0.5 to a black background
  
---

{% include toc title="Contents" %}

# Introduction
This is a project I am doing for a [course][590s]. The project's goal is to simulate computing PageRank in a Pregel like system with multithreading in Java.


# Some Basic Information

## Pregel: A System for Large-Scale Graph Processing
[Pregel Paper][Pregel]

## PageRank
[PageRank Original Paper][PageRank], and 
[PageRank Wikipedia][PageRankWiki]


## Message Queue
[Message Passing with Threads & Queues][mitMessage]

## Review for Java Threads
[Java Threads][threads]


## Important Details about Pregel

### Vertex
- ```compute()``` woule be executed at each active vertex in every superstep.
- ```compute()``` is allowed to query information about the current vertex and its edges and to send messages to other vertices.
- ```compute()``` can inspect and modify the values of out-edges using methods supplied by the out-edge iterator.
- The values associated with the vertex and its edges are the only pre-vertex state that persists across supersteps.

### Message Passing
- Vertices communicate **directly** with one another by sending messages.
- A message consists of **a message value** and the **name of the destination vertex**.
- Messages sent to vertex $$V$$ in superstep $$S$$ are available via an **iterator**, at superstep $$S+1$$.
- There is no guarantee on order of messages in the iterator, but it is guaranteed that messages will be delivered and that they will not be duplicated.

### Aggregators
- Each vertex can provide a value to an aggregator in superstep $$S$$, the system combines those values using a reduction operator.
- Resulting value is made available to all vertices in superstep $$S+1$$.
- Define a new aggregator by creating a subclass of ```Aggregator```, which is a predefined class. And specify how the aggregated value is initialized from the first input value and how multiple partially aggregated values are reduced to one.

In PageRank task, I am considering using ```Aggregator```'s subclass instance(maybe a function is enough for this project) to aggregate message values sent to the same vertex.

[590s]:https://emeryberger.com/systems-for-data-science-compsci-590s/
[Pregel]: https://kowshik.github.io/JPregel/pregel_paper.pdf
[PageRank]: http://infolab.stanford.edu/pub/papers/google.pdf
[PageRankWiki]: https://en.wikipedia.org/wiki/PageRank
[mitMessage]: http://web.mit.edu/6.005/www/fa14/classes/20-queues-locks/message-passing/
[threads]:https://www.ibm.com/developerworks/java/tutorials/j-threads/j-threads.html

