---
title: Research-Note-2
date: 2017-06-17 13:08:32
categories:
- Research Note
- T-mesh
tags:
- Math
- Matlab
---



# Research Note 2

This week, I finish the implementation of refinerec function and also move on to getcoef and findrec. Refinerec is a complicated function and so far I did not find an easy way to reduce the amount of code. 



## Storing Spline on Rectangulation

Getcoef stands for get coefficient. Tensor splines on the rectangulation will be like those on triangulation. We store them by storing the coefficients of Bernstein polynomial on the domain points of each rectangle. As for tensor product, we need dimensions on both x direction and y direction so in total there will be $d \times \widetilde{d}$ domain points for each unit rectangle. However, different rectangles may share some domain points.

Our storing scheme is to separate the long coefficient vector c in to three separate chunks. The first chunk is of length $n_v$, the number of vertices in rectangulation and each store the coefficient on the vertex in the vertex number order. 

The second chunk is for edges. Since there are vertical edges and horizontal edges and number of domain points on these two types of edges are different, we have a long vector with size $(d-1)\times n_{he} + (\widetilde{d}-1)\times n_{ve}$. At first we tried to separate them and store the horizontal ones at first, but later on we decide to just stack them in the edge number order. 

The last chunk is for interior domain points in each recitingulation. So its size is $(d-1)\times (\widetilde{d}-1)\times n_r$. We also store them in the rectangle number order.

Now that we have a long coefficient vector, it will be a problem to just grab the coefficients for a given rectangle. And that's why function getcoef is needed. 

It basically accepts the rectangulation information and a number k and returns the coefficient for domain points in rectangle k.



## Findrec

Findrec is another function. Given a point inside the rectangulation, this function will find the rectangle number that contains this point. 

At first I thought I have a clever way to achieve it. I calculate the vector from the target point to diagonal points of each rectangle and see whether they have opposite direction. This method has time complexity $O(n_r)$, and don't need to build a $k-d\; tree$. However, if there are a lot of points that needs to be found, the time complexity will scale up to that number. Professor Schumaker suggested me to first partition each unit rectangle into two triangles and use the build in tsearchn function. This way the code is faster for more points. 

The example output of findrec is 
$$
\begin{bmatrix}
4 & 6 & 6 & 1 & 3
\end{bmatrix}^{\textrm{T}}
$$
for the following diagram.

<img src="/images/Research/Tmesh/findrec.jpg" width="500">

