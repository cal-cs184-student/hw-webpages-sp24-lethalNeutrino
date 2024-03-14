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
    [PathTracer] Input scene file: ../dae/meshedit/cow.dae                      [PathTracer] Input scene file: ../dae/meshedit/cow.dae
    [PathTracer] Rendering using 8 threads                                      [PathTracer] Rendering using 8 threads
    [PathTracer] Collecting primitives... Done! (0.0006 sec)                    [PathTracer] Collecting primitives... Done! (0.0005 sec)
    [PathTracer] Building BVH from 5856 primitives... Done! (0.0000 sec)        [PathTracer] Building BVH from 5856 primitives... Done! (0.0086 sec)
    [PathTracer] Rendering... 100%! (21.9912s)                                  [PathTracer] Rendering... 100%! (0.1567s)
    [PathTracer] BVH traced 477573 rays.                                        [PathTracer] BVH traced 299751 rays.
    [PathTracer] Average speed 0.0217 million rays per second.                  [PathTracer] Average speed 1.9127 million rays per second.
    [PathTracer] Averaged 805.142177 intersection tests per ray.                [PathTracer] Averaged 8.660702 intersection tests per ray.
    [PathTracer] Saving to file: cow.png... Done!                               [PathTracer] Saving to file: cow.png... Done!
    [PathTracer] Job completed.                                                 [PathTracer] Job completed.
```

Notice how adding in the BVH reduced the render time from 21.9 seconds to 0.156 seconds — a dramatic speedup. (and really satisfying to run) Below is another example, using the teapot:

```                                                                     
[PathTracer] Input scene file: ../dae/meshedit/cow.dae                          [PathTracer] Input scene file: ../dae/meshedit/teapot.dae
[PathTracer] Rendering using 8 threads                                          [PathTracer] Rendering using 8 threads                                  
[PathTracer] Collecting primitives... Done! (0.0002 sec)                        [PathTracer] Collecting primitives... Done! (0.0006 sec)                
[PathTracer] Building BVH from 2464 primitives... Done! (0.0030 sec)            [PathTracer] Building BVH from 5856 primitives... Done! (0.0000 sec)    
[PathTracer] Rendering... 100%! (0.1635s)                                       [PathTracer] Rendering... 100%! (21.9912s)                              
[PathTracer] BVH traced 415430 rays.                                            [PathTracer] BVH traced 477573 rays.                                    
[PathTracer] Average speed 2.5407 million rays per second.                      [PathTracer] Average speed 0.0217 million rays per second.              
[PathTracer] Averaged 738.974202 intersection tests per ray.                    [PathTracer] Averaged 7.048304 intersection tests per ray.
[PathTracer] Saving to file: teapot.png... Done!                                [PathTracer] Saving to file: teapot.png... Done!
[PathTracer] Job completed.                                                     [PathTracer] Job completed.                                                      
```

## Showcasing some speedily rendered complex models:

<p style="text-align:center">
	<img src="./max_planck.png" style="width:45%">
	<img src="./beetle.png" style="width:45%">
	<br>
	The sphere scene generated with 2048 samples per pixel, with 64 samples per batch and a max tolerance of 0.05.
</p>


