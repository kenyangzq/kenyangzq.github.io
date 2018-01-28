---
title: Research-Note-8
date: 2017-08-05 16:34:22
categories:
- Research Note
- Tetra-mesh
tags:
- Math
- Matlab
---

# Research Note 8

This week, I continued to work on the tetrahedron project and implement several functions. 

## Refine1

The refine1 function that I started last week is pretty tricky. There are many edge cases but fortunately I have finished it. 

As mentioned in last note, the way this function works is to find the longest edge of the target tetrahedron and use an internal face to split that edge and also the whole tetrahedron into two smaller ones. These two smaller tetrahedron will always have same volume and share a face. 

The hard part about this function is to keep track of all other list information. For example, after we insert an interior face, we create some two subedges of the longer edge which we split, and we also add two edges on the other two faces. If a neighbor tetrahedron has been refined before, then some of these new edges may already exist, so we have to first check their existence and then update or insert new information. Same process has to be applied to all the list information we keep. 

Another tricky part is the orientation of all the list. For instance, we keep track of a #tetra by 6 matrix for all the edges of each tetrahedron. In order to obtain the right coefficient of spline when we later implement other functions, we need to keep an order among edges of a tetrahedron. So when new edges come, we have to maintain the order. 

I don't want to explain all the details here. The final result is promising. 

## Refine3

On Wednesday, I met with the Professor. After our discussion, we thought a more common way to refine a tetrahedron is to split it into four smaller tetrahedron by connecting each vertex to the center of the original tetrahedron. So I start to work on a new function to accomplish that. 

To be honest, this one is easier than refine1. Since all the new vertex, new edges, and new faces are inside the refined tetrahedron, I no longer need to check their existence and separate the cases. Though orientation is still a problem, with experience in refine1, I successfully implemented this function in this week. 

The way we initialize a partition is always reading from files. It's a simple fact that a cube can be partitioned into 5 tetrahedron, 4 of which are identical and one in the middle, as the following diagram shows.

<img src="/images/Research/Tetra/tetra0.jpg" width="500">

After we refine the first tetrahedron using refine3 the output will be like:

<img src="/images/Research/Tetra/tetra1.jpg" width="500">

