---
title: Research Note 1
date: 2017-06-10 16:11:10
categories:
- Research Note
- T-mesh
tags:
- Math
- Matlab
---

# Research Note 1

　　On Friday morning, I met with Professor Schumaker to discuss further about the code I wrote. Till this point, I have finished the initrec and reclist function, which together will create a long list containing information needed for the rectangle partition, or I personally called it *rectangulation*. 

　　We decided to combine these two functions into one and separate the labeling and plotting into a new function called plotr. With the help from Shiying, I improved the code by vectorizing all the loops. 

　　Now we finally move on to refinerec function. This function will take all the component of our reclist output, and a integer k, indicating which rectangle to refine. The refinement is pretty simple, we just cut the rectangle in half, into two equivalent smaller rectangles. We always cut the longer edge of the rectangle to avoid thin long ones. We also need to update the whole list to preserve the meaning of each components.

　　The idea sounds pretty simple, but the actual code is far more tricky than I expected. First, in our convention, we no longer preserve the original rectangle in our long list. In fact, we do not keep any *superrectangles*, i.e. those that contain subrectangles. We acchieve it by updating the original rectangle and adding a new one in the list. For vertices, refining one rectangle may add a *hanging vertex*. A hanging vertex is a vertex on an edge that is not an intersection. For example, vertex 8 in the following rectangulation is a hanging vertex. 

<img src="/images/Research/Tmesh/hangver.jpg"></img>

However,  refinement may also change a hanging vertex into a regular interior vertex. For instance, if we refine R2 in the above rectangulation, V8 will no longer be a hanging vertex.

　　For edges, in our refinement, we need to add new short edges and check whether all of them are edges of at least one rectangle. In our convention, we only store edges that are sides of rectangles that don't have subrectangle. For example, in above figure, edge that connects V3 and V4 is kept after refinement. But if we refine R2, then that edge should be removed. 

　　One refine example is demonstrated here. We start with 2 rectangles.

<img src="/images/Research/Tmesh/recref1.jpg" width="400"></img>

Then we keep refining the first rectangle three times. The result is as followed:

<img src="/images/Research/Tmesh/recref2.jpg" width="400"></img>

<img src="/images/Research/Tmesh/recref3.jpg" width="400"></img>

<img src="/images/Research/Tmesh/recref4.jpg" width="400"></img>

　　The list cannot be shown due to confidentiality. So far the code is 500+ lines, and I have not yet finished.

　　There are hundreds of other small details that I have to focus on while implementing the code, like the keep tracking of left and right rectangles for each edges, subset relation between edges and orders of edges. So far in the first week, we are still at geometry phase, not touching any spline yet. After the refinerec function is complete, we will start to store spline on domain points and get into the real meat. 

​	

