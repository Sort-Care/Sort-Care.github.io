---
layout: single
title: Looping through 2D Grid Neighbors
date: 2019-09-06 10:47 +0800
mathjax: true

excerpt: "Using Arrays for calculating coordinates changes."

header:
  teaser: /assets/images/circle.png
  overlay_image: /assets/images/circle.png
  overlay_filter: 0.4
---
{% include toc title="Contents" %}

# Enviroment Setting
Say we have a 2D grid with $R$ rows and $C$ columns. Assume $(r,c)$ represents the current cell we are processing.
There are many cases where we need to find out all neighbors of this cell and loop them into a subroutine. What will
be a quick and precise way to loop through all of them and at the mean time testing if a coordinate is valid?

# Neighbor Coordinate Changes
First, we use two array to store the relative change of all neighbors.

```cpp
int dr[] = {1, 1, 0, -1, -1, -1,  0,  1};
int dc[] = {0, 1, 1,  1,  0, -1, -1, -1};
// (dr[i], dc[i]) pair gives one possible neighbor relative coordinate.
// ordered: S, SE, E, NE, N, NW, W, SW
```

   | :-:   | :-:  | :-:  |
   | -1,-1 | -1,0 | -1,1 |
   | 0,-1  | r,c  | 0,1  |
   | 1,-1  | 1,0  | 1,1  |

given this setting, we can easily loop through all possible neighbors. Next we will get to how to quickly check if within bounds.

# Validate Coordinate
Assume $(r, c)$ now contains the new coordinate of a possible neighbor, which is calculated base on above relative neighbor position.
We can use the following function to quickly test if it is still valid:

```cpp
bool validate_coordinate(const int R, const int C, const int r, const int c){
  if (r < 0 || r > R || c < 0 || c > C) return false;
  return true;
}
```

# Application: Flood Fill
This is a very common problem of finding and counting the number of connected parts. The following approach is a depth first one. A bfs one works
very similarly. It is also called coloring in CS terminology. It is also known as "flood fill" and usually performed on implicit graphs.
```cpp
int floodfill(int r, int c, char c1, char c2) { // returns the size of CC
  if (r < 0 || r >= R || c < 0 || c >= C) return 0; // outside grid
  if (grid[r][c] != c1) return 0; // does not have color c1
  int ans = 1; // adds 1 to ans because vertex (r, c) has c1 as its color
  grid[r][c] = c2; // now recolors vertex (r, c) to c2 to avoid cycling!
  for (int d = 0; d < 8; d++)
    ans += floodfill(r + dr[d], c + dc[d], c1, c2);
  return ans; // the code is neat due to dr[] and dc[]
}
```
