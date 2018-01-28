---
title: Research-Note-6
date: 2017-07-23 16:06:47
categories:
- Research Note
- T-mesh3D
tags:
- Math
- Matlab
---



# Research Note 6

This week we debug the 2d code and keep working on 3d functions. 

In 2d case, Professor Schumaker came up with a function to add C1 smoothness condition between rectangles. This function makes use of recl and rec, vectors denoting the rectangles on the left and right of each edge. Unfortunately, due to the way we develop refinerec, there is quite a few bugs in the function. So I spent rought two days on debugging the refinerec function and finally get a version that pass enormous test cases. 

## Interdp3d and Valtspgrid3d

On the 3d side, I develop some functions as the extensions of their 2d correspondences. Two of them are interdp3d and valtspgrid3d. 

Interdp3d interpolate a given function on the whole rectangulation and return the coefficient as a 3d Matrix. Since Matlab does not support nested list structure, we look to *tensor toolbox* for help. A tensor is just a n dimension matrix. The toolbox include basic functionality such as addition, subtraction, scalar multiplication and scalar division. It also supports multiplication between tensor and matrix on a given direction. That's what we mainly use. 

Back to my functions. Interdp3d returns a 3d tensor storing the coefficient of the 3d spline on the rectangulation. Then I use valtspgrid0 to obtain approximation of values of the given function at 3d grid points using the coefficient we get. To varify the result, I compute the norm of error terms. 

We haven't found a good way to visualize the function. So far we have try to plot the isosurface. There is a nice package on MathWork forem named sliceomatic. It gives a live isosurface plot, but it still does not meet our expectation.



## Other functions

Since my refinecube function only returns lists of relation among cubes, faces, edges and vertices, I need to find a way to get the other indispensible information such as the hanging vertices, subedges, etc. 

So I wrote a bunch of small helper functions, like gethv for get hanging vertex, getse for get subedges, getsf for get subfaces and gethe for get hanging edges. Some of the cases are pretty tricky, for example in 3d a vertex can be hanging on multiplt edges. 

Now that I prefer not to update these other important list in the refinement function, that function will definitely be less error-prone. However, in the view of efficiency, separating them out will without doubt be slower. So far it has not been a problem. 



## tsp0smooth3d

Now I am currently working on the tsp0smooth3d function. It enforces the C0 smoothness condition on the 3d partition. Compared to the 2d tsp0smooth I implemented half month ago, this one is much more complicated. In 2d, only two cases are in consideration, hanging vertices and subedges. On the other side, in 3d, there are at least 4 cases, hanging vertices, hanging edges, subedges and subfaces. And each cases is much harder to implement, since we have higher degree of Bernstein basis polynomial. 

It's not that easy to describe the problem I ran into while implementing this code. The vital issue is that I cannot just easily visualize the work. Most of the cases need pure imagination. When 3d is involved, things always get more complicated. 

I hope I can finish it in few days next week. 



## P.S.

One interesting and weird thing I noticed about Matlab is its meshgrid function. In 3d cases, one needs to do 

​				`[X,Y,Z] = meshgrid(ty,tx,tz)`

to get the correct mesh in 3d, where ty, tx, tz are generated using linspace on their corresponding direction. Notice the weird order of argument. I am still wondering why these people prefers to pass the argument in this way. Just because of this ordering, I spent 2 more hours debugging my code. 