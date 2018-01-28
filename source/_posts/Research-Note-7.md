---
title: Research-Note-7
date: 2017-07-30 20:01:42
categories:
- Research Note
- T-mesh3D
tags:
- Math
- Matlab
---

# Research Note 7

This week I first put the 3d function pieces together to make a adaptive refinement code penalizing with C0 smoothness condition. The whole program is based on the 2d version that Professor Schumaker wrote. 

## Tadpat03d

The program is called Tadapt03d, where T for tensor product, adapt0 means adaptive fitting with C0 smoothness and 3d indicates that it's on 3d cubes. 

Same as usual, the program begins with calling the list function reclist2 to get list of information of the partition. And a function handler is created, which is our target function to approximate. 

It then called the *interpdp03d* function to interpolate the given function f with C0 smoothness condition enforced. The *tsp0smooth3d* function, which return the smoothness condition matrix M, is called inside the *interpdp03d* function. Now that we obtain the coefficient vector $c$, we start to refine. 

The criterial is to calculate the error of approximation in each unit cube, and find the biggest 20% of all the cube to further refine. I wrote an error calculating function called *rerr13d*, which gives the errors in each cube. We then refine those cubes with large error. I would prefer to call them a single *batch*. After we refine all the cube in the batch, we call the *interpdp03d* function again to obtain the new coefficient vector $c$. Then we start another iteration. 

The number of *batches* or level of refinement is given by user input. 

We still haven't found a good way to visualize 3d functions so now I can only use error to check my program. 

One thing to notice, at this point the *tsp0smooth3d* function is actually half-complete. As mention in last note, there are at least four cases in 3d C0 smoothness condition. I still haven't figure out the right way to put in hanging edges and hanging vertices on multiple edges. So the function actually miss some of the condition. 



## Tetra-mesh

### Plot and readfile

On the other hand, I have started the tetrahedron project. This one will be the final project of this summer, and I believe it will be more challenging than the previous two. 

As I did in the previous two projects, I started by looking for a way to visualize  tetrahedrons. The function *patch* is still a reasonable choice. As I learn from visualizing cubes, I did basically the same — wrote a function to plot all the tetrahedrons given a matrix of points and a matrix of vertices of each tetra. 

For tetrahedron, it's more like the 2d triangle case. It's hard to just initialize a bunch of tetras with few arguments. So I prefer to read in the files like Professor Schumaker did in his splinepak. I wrote a *readtetra* function to read in formatted tetrahedron data and get the basic information about the tetrahedron group. 

### List function

Now that I've got a working plot function and a read file function, I move on to the first cornerstone — list function. 

Similar to both the cubelist and trilist case, I need to get all the needed information based on the very basic list I read from files. Specifically, I need a matrix E for tetra edge relation, a matrix F for tetra face relation, a matrix evertex for edge vertex relation, a matrix fvertex for face vertex relation and so on. 

One tricky thing that's unique for tetrahedrons is their orientations. For rectangles and cubes, it's pretty easy to figure out how to put the edges and vertices in order. But for tetrahedron, so far I haven't get a nice and easy way to do the same. 

I've tried to orient the edges in such a way that three edges of one face go first in an order that obey the right hand rule, and then the other three edges go next with same order. But later in my refine function, things become more complicated and I decide to leave such idea behind now. 

It took me about two days or so to get a nice working list function. 

### Refine1

A tetrahedron can be refined in several ways, one into two, three or four. I started with the easiest, find the longest edge and use a interior face to split the edge and the whole tetra into two small ones.

I am still working on this function. There are some tricky place that needs to be handled. I believe by next week, I will have a nice working refine1 funcion ready.