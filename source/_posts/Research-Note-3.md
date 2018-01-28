---
title: Research-Note-3
date: 2017-06-23 13:38:05
categories:
- Research Note
- T-mesh
tags:
- Math
- Matlab

---

# Research Note 3

This week, we finally get into the meat, spline. After I have got the basic geometric functions to work, we can move on to evaluate splines on rectangulations. 



## Interpolating Functions

As I have discussed in the last Research Note, a spline on the rectangulation is stored as a long coefficient vector. That vector is separated into three chunks for vertices, edges and interior domain points respectively. But  where are these coefficients coming from? How are we going to interpolate functions on rectangulation?

Similar to what happens on triangulation in 2D space, we construct the coefficient vectors by looping over all the unit rectangles, calculate the coefficient, and put it in the right place in long vector $c$.

We solve the interpolation problem same as we did in any other cases, except we are using Bernstein polynomial as basis functions. First I use a function to calculate all the value of Berstein polynomial on $d+1$ equally spaced point on $[0,1]$ to get a matrix $B$, where $d$ is the degree of the $x$-direction polynomial. Same operation for $y$ direction and I get another matrix $\widetilde{B}$ using $\widetilde{d}$, the dimension for $y$-direction polynomial. Then I calculate the value of the target function $f$ at the $(d+1)\times (\widetilde{d}+1)$ grid points to get a value matrix $V$. Finally I solve the linear system to get the coefficient matrix $C$. 
$$
B\times C \times\widetilde{B} = V
$$
After I got the right coefficient, I use the function *getindex* to find the index of $c$ to put the coefficient in. And all that above is the first major function I worked on this week. It's named *interdp* for interpolating domain points.  

Note, here our work can only handle functions on a rectangulation without a hanging vertex. For refinement of rectangulation, we need to construct smoothness conditions and solve for the corresponding coefficients. That will be the major part of my work next week.

#### Example

A quick small example. Suppose we are interpolating function $f$ with $d=3$ and $\widetilde{d}=4$ on unit square. 
$$
f = x^2 + 2y^3
$$
The result plot on the 3 by 3 rectangulation is 

<img src = "/images/Research/Tmesh/interdp.jpg" width="500">

And the root mean square error of the resulting surface is $1.7\times 10^{-16}$.



## Interpolation of Partial Derivatives 

After I tackle the problem of interpolate a general function on the rectangulation. I start to work on the partial derivatives of a general function. 

I start by experimenting on a tensor product Bezier patch, or just a rectanlge with Bernstein basis polynomial. Solving the interpolation problem for the partial derivative of a function is equivalent to taking the partial derivative of the Bernstein basis polynomial and adjust the coefficients. 

With basic linear algebra, one can easily find out that differentiating a matrix of basis function is same as multiplying a forward difference matrix. Since the interpolation solution is given by the product of three matrices, we can do the forward difference on any one that contains the variables. Professor Schumaker prefers to compute a lower degree Bernstein basis polynomial matrix and do the forward difference on the coefficient matrix $C$. 

Eventually, we want to have a function that can give the values of the partial derivatives of any degree of a function on a grid. That will be another task for me in next week.





