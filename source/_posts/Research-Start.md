---
title: Research Start
date: 2017-06-06 23:22:00
categories:
- Research Note
- T-mesh
tags:
- Math
- Matlab
---

## Research Start!

　　Today is the first day of my 2017 summer research. Under Professor Larry Schumaker's guidance, I will work on several projects in the following two and half month. Basically all of them belong to numerical analysis. In particular I am building libraries for special spline computation purpose. All the code will be written in Matlab.

　　The first project I will work on is the rectangular tensor product spline library for hanging vertex, which is also referred to as **T-meshes** in spline literature. I will first implement functions to create 2D rectangle partition and generate all the needed information such as the edges for each rectangle, or whether a given edge is a boundary edge. These first few functions are only geometric and Matlab problems. But as we move on, we will do exactly the same as we are on triangles in 2D planes. I will create the domain points and determine sets for any partitions, store all kinds of splines, compute polynomials and add smoothness at edges and vertices. Finally, when we are comfortable with 2D cases, we will move on to 3D space. Note, all the code should support hanging vertices.

　　I have already gotten the assignment for the first week. I need to implement code to generate nice looking rectangle partition for any given number of vertices on x axis and y axis, like the following for which (3, 4) are passed as arguments. 

<img src="/images/Research/Tmesh/rec34.jpg" width='500'>

Then I also need to write a complicated code to get all the information needed of the partition. Since Professor Schumaker may use my code for his monograph and commercial purpose, I cannot disclose the detail of the code. The last task for the first week is to write a code to refine a given rectangle in the partition and update the list. The refinement can be define in any manner and it may lead to hanging vertex.

