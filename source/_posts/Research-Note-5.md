---
title: Research-Note-5
date: 2017-07-15 12:38:58
categories:
- Research Note
- T-mesh
tags:
- Math
- Matlab
---



# Research Note 5

I was seriously ill last week so I haven't worked a lot and updated my Research Note. 

This two week I keep working on the T-mesh 3D functions. By today, I have finished the cubelist and refinecube function. These two function corresponds to those in 2D case, a.k.a. reclist and refinerec. In particular, refinecube is much more complicated than refinerec so I took a completely different approach on writing it. I wrote three helper functions to update information about vertices, edges and faces respectively. 

Other than the 3d functions, now that we have finish most part of 2d cases, we can finally create some sophisticated program on top of these Bernstein basis polynomial on rectangulation. 

For example, in this week, Professor Schumaker had come up with an adaptive fitting program using the functions we have written last month. 

The program fit a function on a rectangulation, calculate the error in each iteration and refine those rectangles that will decrease the error in greatest extent. 

An output case: 

​	For fitting function 
$$
f(x,y) = \tanh(40y-80x^2)-\tanh(40x-80y^2)
$$
with a initial 5 by 5 rectangulation using Bernstein basis polynomial of degree 5 on both direction and with a 50 level adaptive fitting, we get the final rectangulation as

<img src="/images/Research/Tmesh/adapt1.jpg" width="500">

The function plot and the error plot are

<img src="/images/Research/Tmesh/adapt2.jpg" width="500">

<img src="/images/Research/Tmesh/adapt3.jpg" width="500">

