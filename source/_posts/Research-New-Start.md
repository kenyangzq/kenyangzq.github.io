---
title: Research-New-Start
date: 2017-08-26 17:19:10
categories:
- Research Note
- Tetra-mesh
tags:
- Math
- Matlab
---

# New Beginning

By the end of summer, my research project officially ended. In last week, cooperating with Shiying Li, PhD student in Math, we came up with a Tetrahedron adaptive program. I will summarize our work in last week in this note. 

## T3adaptive

In previous week, I have already finish this new refinement function named *refinetetra3* which will choose the largest boundary face and split it in 3 to get three new sub-tetrahedrons. Later in fall, I plan to finalize it so that it can split internal faces.

The remaining puzzles are interpolation functions, grid error evaluations and an error function to choose the tetrahedron to further refine. Luckily, these functions are just extensions from the 2D cases. And I have gathered enough experiences on them in 2D box project and 3D cube project. Therefore, it only took me 1 whole day to finish them.

After we have gathered all the tools, it's finally the time to put everything up together. 

In the first part of our big program,  we need to initialize the big partition. Other than reading from files like we did before, I also wrote a small initialization function to create Delaunay partition on 3D box of any size. 

Then we use our good old friend, list function *tetralist* to get all the partition information we need. 

Suppose we are given a target function, now that we have all the parts ready, we performed a first interpolation on the unrefine setting, which gives us a long coefficient vector $c$. Then we can start our main refinement process. 

In each iteration, we did the following

1. Based on the current coefficient, we calculate the error scaled by the volume in each tetrahedron using the error function. By error we mean the max error inside the tetrahedron. 
2. Then we sort the error and find the largest 20% and store their indices in a list.
3. For every tetrahedron in the list, if it's on the boundary, we use *refine3* to refine one of its boundary faces; if it's a interior tetrahedron, we perform a Alfeld refinement using *refine4*.

We predefined the number of iteration. If in an iteration we have the max error of all tetrahedron less than a very small number, say $10^{-5}$, we exit the loop.

After the loop, we use grid error evaluation to estimate the error and plot the final refinement.

In this refinement program, we can get exact result for any trivariate polynomial given that the degree of basis Bernstein polynomial is big enough, which is what the approximation theory shows.

Since we still haven't found a nice way to displace 3D functions, I don't have a nice plot to show here. 



## New Start

As I mentioned, my research has officially over, but there are still lots of works that need to be done. So far no tetrahedron spline library exists and more functions are needed. 

I have decided to continue working with Professor Schumaker on tetrahedron spline library in the fall semester. And I also plan to write a honor thesis on this topic to satisfy the requirement of graduating with honor in Math. 

Since I have not enrolled in independent study or formally work as a RA, I can only use my spare time on this project. I will write a research note once every month instead of every week.

















