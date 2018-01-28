---
title: SVM
date: 2017-06-18 23:41:42
categories:
  - Study Note
  - Machine Learning
tags: 
  - Machine Learning
---

# Support Vector Machine

Chapter 9 of Statistical Learning. Starts form Maximal Margin Classifer, to Support Vector Classifier and finally to SVM. 

## Maximal Margin Classifier

In a p-dimension, a *hyperplane* is a flat affine subspace of dimension p-1. For example, in two dimension, a hyperplane is a line. 

One intuitive way is to use hyperplane to classify data points. But there are inifinite way given it's possible to find one hyperplane. So find the hyperplane with the greatest minimal vertical distance from data point, named *maximal margin classifier*. 

Note:

1. The maximal margin hyperplane depends only on the few closest data points, which are called *support vector*. 
2. It may change a lot due to one data point. 

### Construction

Maximal margin hyperplane is the solution to the optimization problem:
$$
maximize\;M	\\\\
subject\; to \; \sum_{j=1}^p \beta_j^2 = 1 \\\\
y_i(\beta_0+\beta_1x_{i1}+...+\beta_px_{ip}) >= M \; \forall\, i= 1,...,n
$$
The second equation ensure that the vertical distance from any data point to the hyperplane is given by 
$$
y_i(\beta_0+\beta_1x_{i1}+...+\beta_px_{ip})
$$
So together, they ensure that each observation is on the correct side of the hyperplane and at least a distance M from the hyperplane.

However, the data points may not be separable. Then generalize it to *support vector classifier*.

## Support Vector Classifier

Consider a classifier based on hyperplane but not perfectly separate the two classes in the interest of 

- Greater robustness to individual observations 
- Better classification of most of the training observations.

### Construction

In this case the optimization problem becomes:
$$
maximize\;M	\\\\
subject\; to \; \sum_{j=1}^p \beta_j^2 = 1 \\\\
y_i(\beta_0+\beta_1x_{i1}+...+\beta_px_{ip}) \geq M(1-\epsilon_i) \; \forall\, i= 1,...,n \\\\
\epsilon_i \geq 0, \; \sum_{i=1}^n\epsilon_i \leq C
$$
where C is a nonnegative tuning parameter, can be thought as a budget for the amount that the margin can be violated by the n observations. When C is 0, it becomes maximal margin hyperplane optimization problem. In practice, C is generatlly chosen by cross-validation. It controls the bias-variance trade-off. 

Similar to MMC, SVC has a nice property: only observations that lie on the margin or that violate the margin will affect the hyperplane. So if an observation lies strictly on the correct side of the margin, it does not affect the classifier. Observations that lie on the margin or wrong side of margin for their class are known as *support vectors*.



## Support Vector Machine

One way to convert a linear classifier into non-linear is by enlarging the feature space using quadratic, cubic or higher polynomial functions of the predictors. The resulting decision boundary is linear in the enlarged feature space, but not in the original one. However, sometimes we may end up with a huge number of features and increase computation. 

SVM is a way to enlarge the feature space but leads to efficient computation. It uses *kernels*. 

The solution to support vector classifier problem involves only the inner products of the observations but not observations themselves. It can be shown that the linear support vector classifier can be represented as 
$$
f(x) = \beta_0 + \sum_{i=1}^n \alpha_i\langle x,x_i\rangle
$$
where there are n parameters $\alpha_i$, one per training observation.

It turns out that only the $\alpha$ of support vectors will be nonzero, so if S is the collection of indices of support vectors, then the solution is
$$
f(x) = \beta_0 + \sum_{i\in S}\alpha_i \langle x,x_i\rangle
$$
Now if for every appearance of inner product, we replace it with a generalization of the inner product 
$$
K(x_i,x_{i'})
$$
where K is some function that we call it *kernel*. It quantifies the similarity of two observations. We can choose it to be inner product, which gives SVC. This is known as a linear kernel. Another choice is 
$$
K(x_i,x_{i'})= (1+\sum_{j=1}^p x_{ij}x_{i'j})^d
$$
This is known as a *polynomial kernel* of degree d, where d is a positive integer. It will give a much more flexible decision boundary. It essentially amounts to fitting a support vector classifier in a higher dimensional space involving polynomials of degree d. 

When the support vector classifier is combined with a non-linear kernel, the resulting classifier is known as a support vector machine. 

The advantage of using kernel is computation. It only needs to compute the kernel function for all pairs of observation. 

## Extension

The SVM can be extended to multiple classes case. One vs One or One vs All.

SVM has a similar loss function as logistic regression, which often performs better, so SVM is not that widely used nowadays. The kernel trick, though it can also be applied to other classifier, is most used in the context of SVM.







