---
title: Research-Note-4
date: 2017-07-02 15:10:06
categories:
- Research Note
- T-mesh
tags:
- Math
- Matlab
---



# Research Note 4 

This week my Professor Schumaker left for a conference, and won't be back until next Friday. Before leaving, he has assigned me several tasks. 

## Tmesh Functions

The very imminent one is to continue work on the Tmesh functions. We need a function to perform penalize least square approximation on refined rectangulations. It consists of two major steps: 

1. Getting the smoothness condition between unit rectangles.
2. Perform penalize least square approximation.

### Smoothness Condition Matrix

Since we have refining our rectangles, we may have very weird partition of the original domain region. In order to obtain a smooth surface after we approximate a function, we need to calculate the smoothness condition on sharing edges and sharing vertices. 

The idea is not very complicate. First, we obtain the number of smoothness condition $ns$ we need by counting sharing edges and sharing vertices. The formula also involves the dimensions on x, y directions. 

Then we construct a $ns$ by $nc$ zero matrix, where $nc$ here is the number of coefficient for a spline on the rectangulation. We can use the *sparse* function in Matlab to save storage space, since the smoothness condition only involves the coefficients on the same edge. 

To get the value at specific entry, we need to traverse all the shared edges and hanging vertices. Note the order we put the smoothness condition in each row does not matter, because when we enforce the condition, we will simply do it by minimize the product of $cM$ where $c$ is the spline coefficient and $M$ is our matrix.

As we have different degree of polynomial on different direction, I chose to first traverse all the horizontal edge and then vertical. In each iteration, I get all the coefficients of domain points on the current edge, and then solve for a linear system. This system represent the fact that the Bernstein polynomial on different side of the edge reach the same value on the domain points. In other words, this is also a interpolation problem, where I represent the coefficient of the Bernstein polynomial using the coefficient on the other side. We basically do the same on all the hanging vertices. 

Finally, the function return the well-constructed matrix $M$ as output.



### Penalize Least Square Approximation

This function is a little similar to the one that interpolate any given function, except for the fact that we are solving least square fitting problem accompany with smoothness condition. 

I have finished the outline of the function and there is a problem that I have to wait till Professor get back. Basically I have to use the Kronecker product of Bernstein polynomial to get the linear system. I will add details about this function in next Research Note.



### Refinerec

In addition to the two functions above, I also spent two whole days on debugging and fixing *refinerec* function. 

Professor notice a significant bug on the previous version. It's pretty complicated to articulate the bug. I added 250 lines and finally fixed it. Now the whole refine function is 750 line, but I believe I could have combine some of the cases or extract out some helper functions to make the whole code more readable.



## Other Directions

Other than the Tmesh functions that I need to work on, Professor pointed two direction for me in the next two weeks. First is the 3D version of the Tmesh problems. In other words, I need to extend our current work on 2D to 3D. We both know that I will be a huge amount of work, so he only expects me to first get all the geometric functions. 

The other direction is 3D Tetrahedron partition. This one is even harder since we can't easily visualize what happen in 3D space. So I chose to first work on the 3D Tmesh direction. 

### 3D Tmesh

Till this Sunday, I have finish several function we need. The path along which I work on these function is pretty straightforward. First I think we need to come up with a way to visualize the boxes in 3D. I found the *patch* function in Matlab and implement a simple function to display a cube. One example with label is as follow

<img src="/images/Research/Tmesh3D/reference.jpg">

Then I need a function that can plot any number of cube partition together, which calls on the simple plot function. Since we will definitely need a 3D version of *reclist*, which I named it *cubelist*, I choose to use the output from the list function to plot the cubes. 

In 3D there are a lot of possible output containing information about the whole partition that we may need. Thus I need to think carefully before I move on. We have 4 different dimension of objects: vertices, edges, faces and cubes (or boxes). First and foremost, given $\textbf{[nx, ny, nz]}$ as a parameter for the initial partition, we inevitably need to output the coordinate of each vertices, and that is $\textbf{[x,y,z]}$, three outputs. Second, we need to have the relation between cubes and vertices, cubes and edges, and also cubes and faces; they are three matrices, **V** of size $ncube$ by 8, **E** of size $ncube$ by 12 and **F** of size $ncube$ by 6. In these matrices, we use the vertices number, edge number and face number to represent each cube. 

After that, we need the relation between edges and vertices, between faces and edges, and between faces and vertices. These matrices can help us quickly get the information we need. I named them as **evertex**, **fedge** and **fvertex**. 

Finally, I think in the future, we also need some opposite relation, which are matrices that connect vertices and cubes, connect edges and cubes, and connect faces and cubes. They help me to find the cube that are arround a given vertex, edge or face. In addition, they can also be used to find the boundary vertices, edges and faces as we put zero for nothing (or boundary). I call these matrices **vstar**, **estar** and **fstar**. 

All these output I chose is entirely based on my own experience. Therefore, after Professor Schumaker comes back, we decide whether to keep all of them and add some more, or dump some of them. 

With these output, it's far more than enough to generate a plot of the whole partition. Here is a 3 by 3 by 4 partition, where the numbers are points on each side.

<img src="/images/Research/Tmesh3D/cube1.jpg">

It's possible to labe all of the object but that will be too cumbersome and messy especially when we get to more partition.

I have already finish the *cubelist* function now, and I decide to move on to the hard part, the *refinecube* function in next week. Wish me good luck!

