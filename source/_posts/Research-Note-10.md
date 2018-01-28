---
title: Research-Note-10
date: 2017-09-10 22:25:22
categories:
- Research Note
- Tetra-mesh
tags:
- Math
- Matlab
---

# Research Note 10

Last week I was seriously illed so I did skip the note. Since my school starts, I can only use my leisure time on this research project, so without doubt, my progress is much slower compared to in summer. However, in last week, I have made some breakthrough and I thought it's worth noting. 

### C1 Smoothness Condition

Right now our main goal is to solve scatter fitting problem using tetrahedron partition in 3D space. Professor Schumaker has a outline to attempt this problem. We first need to partition the space using tetrahedron, then we need to figure out how to enforce C1 smoothness condition between each unit tetrahedron. After that, we can use the idea of minimum energy by adding a penalizing terms of the smoothness condition. If we can reach this far, we can then choose the right parameter and solve the problem. 

Now the only missing key is to get the C1 smoothness condition. By C1 smoothness, it means that splines between different tetrahedron not only join on the sharing faces, but the directional partial derivative also agrees on the border. Since we are representing splines with coefficent at domain points, it can be proved that if the coefficient on the sharing faces agree, we will have C0 smoothness condition. And from that on, with one more layer, or I should say internal faces, of coefficient agree, we will one degree higher smoothness condition. 

Enforcing coefficient to agree on the sharing face is easy. Because of the way we store the spline, it automatically satisfy the C0 smoothness condition. C1, on the other hand, is far more tricky. It involves lots of geometric manipulation of the coefficient tetrahedrons. 

Long story short, Professor Larry came up with several functions to get the C1 smoothness condition with d = 3. The idea is to loop over all the interior face. For each face, get the left and right tetrahedrons' coefficient. Perform some geometric manipulation on them and obtain the internal layer of coefficient by rotations. Then enforce them to agree by setting up a linear system of equation. 

After he show us the code, I spent a few days studying it. And in today's afternoon I combined his functions with my getindextetra and other helpers. With some struggles, I believe I have extended the functions to give C1 smoothness condition for any value of d. So far these functions pass all the test cases I have. This means we have obtained the final missing piece to solve scatter data fitting problems. 

Probably in next week, we will put all the pieces together and finally obtain applicable solver. 