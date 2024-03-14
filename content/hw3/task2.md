+++
title = "Bounding Volume Hierarchy"
template = "page.html"
[extra]
mathjax = "tex-mml"
+++
 
## Making our renderer go brrrrr 

Before implementing rendering acceleration with a BVH, our pathtracer used a naive algorithm wherein every ray would be checked against every `Primitive` in the scene, leading to a very slow $O(nm)$ runtime with $n$ primitives and $m$ rays. To speed this up, we used a BVH constructed with the median heuristic. We sorted the list of `Primitive`s based on the longest axis of the bounding box, and then used the median as the split point. Another heuristic we implemented was the midpoint heuristic, but both produced approximately equal results from our testing, so we kept the simplicity of the median approach to avoid the edge case of creating an empty node. To construct a BVH, we performed the following algorithm:

1. Generate a `BVHNode` containing all of the primitives.
2. If the list of `Primitive`s passed is has less elements than `max_leaf_size`, return a `BVHNode` containing all of the `Primitive`s.
3. Otherwise, compute the size of the bounding box of the `BVHNode`, and choose the maximal axis.
4. Sort the list of `Primitive`s based on their position in the maximal axis, and split into two equal lists.
5. Set the `l`(eft) member of the `BVHNode` to a `BVHNode` constructed from the first half of the list, and the `r`(ight) member to be a `BVHNode` constructed from the second half of the list.

## Comparing Rendering Times 

Below, we compare output times of rendering the (really cute) Cow with and without BVH acceleration:
```
does lined code work
```

## Showcasing some speedily rendered complex models:
