---
title: Research-Note-9
date: 2017-08-13 12:00:14
categories:
- Research Note
- Tetra-mesh
tags:
- Math
- Matlab
---

# Research Note 9

This week we mainly work on the tetrahedron project. Besides those little helper functions, I implemented two new main components of our project.

First is another new refine function. We have decided to renamed the previous refine function of tetrahedrons. The new one split a tetrahedron into 3 new tetrahedrons by finding the largest facet and its center point, and connect the opposite vertex to that center point to insert 3 new internal faces. In that way an old tetrahedron is splited into 3. 

The implementation is quite similar to the previous function and only took me one day to finish this function. 

<img src="/images/Research/Tetra/refine3.jpg" width = "500">

Here is a demonstration of the refinement. 

## Stochastic Walk

Another function is called stochastic walk. In the final stage of our project, we will have many points in our large tetrahedron partition, and we need to locate each point, aka, find the tetrahedron contain a given point. 

Matlab has a built in function called pointLocation, which should accomplish that. However, I tested it with some simple cases and find that it fail in some. I have posted a question on the MathWork forem but receive no response so far. 

So we decide to implement our own pointLocation function. The very first and foremost idea is to use the barycentric coordinate's property. If a point is located inside a tetrahedron then the barycentric coordinate of that point relative to that tetrahedron will have all positive components. However, this means we need to calculate the barycentric coordinate of a point for all the tetrahedron. This is too inefficient. 

Another way is to build a K-D tree and search on the branch. However, K-D tree fails in some cases, e.g. triangulation with holes in it. 

I did some research on Google and found a paper introducing some nice way to find point location. The paper is called *Walk in a Triangulation* by Olivier, etc. It can be found <a href="https://hal.inria.fr/inria-00072509/document">here</a>.

In the paper, there are 4 different ways to find point location. The idea is quite similar. Start from a tetrahedron, according to some condition, move to next tetrahedron and so on, and finally stop in the target tetrahedron. 

I choose to implement the Stochastic Walk as it's easier to implement and we have the available helper functions. It starts from a random tetrahedron. Choose a random facet of it, and check whether the point is on the other side of the facet. If so, move to the next tetrahedron sharing that facet; if not, move to another facet of the current tetrahedron. In the mean time, it also 'remember' the last tetrahedron visited so that we won't ends up in an infinite loop in a pair of tetrahedron. 

There are also some other little details, but I don't want to discuss it here. 