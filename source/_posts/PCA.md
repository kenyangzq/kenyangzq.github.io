---
title: PCA
date: 2017-06-18 23:45:11
categories:
  - Study Note
  - Machine Learning
tags: 
  - Machine Learning
---

# Principal Component Analysis

PCA refers to the process by which principal components are computed and the subsequent use of these components.

It's a technique to summarize a large set of correlated variables with a smaller number of representative variables.

## Principal components

Principal components are directions in p-dimension feature space that the observations that are as *interesting* as possible. The concept of interesting is meatured by the amount that the observations vary along each dimension. Each of the dimensions found by PCA is a linear combination of all the p features.

The first principal component of a set of features $X_1,X_2,…,X_p$ is the normalized linear combination of the features
$$
Z_1 = \phi_{11}X_1 + \phi_{21}X_2 + ... + \phi_{p1}X_p
$$
that has the largest variance. By *normalzed*, it means that $\sum_{j=1}^p\phi_{j1}^2=1$. The $\phi_{11},…,\phi_{p1}$ are called *loadings* of the first principal component and together they form the loading vector. 

Since we are only interested in variance, we can assume that each variables has been centered to have mean zero. Then finding the loading vector is essentially solving the optimization problem:
$$
\underset{\phi_{11},...,\phi_{p1}}{maximize}\{\frac{1}{n}\sum_{i=1}^n (\sum_{j=1}^p\phi_{j1}x_{ij})^2 \},\, s.t\; \sum_{j=1}^p \phi_{j1}^2=1
$$
Since $z_{i1} = \phi_{11}x_1 + \phi_{21}x_2 + ... + \phi_{p1}x_p$ and $\frac{1}{n}\sum_{i=1}^nx_{ij}=0$, we have the average of $z_{11},…,z_{n1}$ is zero. So what we are optimizing is just the sample variance of the n values of $z_{i1}$. We call $z_{11},…,z_{n1}$ the *scores* of the first principal component. 

After the first principal component $Z_1$ is determined, we can find the second principal component $Z_{2}$. The second principal component is a linear combination of $X_1,…X_p$ that has maximal variance out of all linear combinations that are *uncorrelated* with $Z_1$. It turns out that constraining uncorrelated is equivalent to constraining the direction to be orthogonal. The other principal components can be computed the same way.

Once we have computed all the principal components, we can plot them against each other in order to produce low-dimensional views of the data. 



## More on PCA

### Scaling the Variables

Before the PCA is performed, the variable should be centered to have mean zero. Furthermore, the results obtained is dependent on whether the variables have been scaled individually. 

In addition, **the standard deviation is usually scaled to one before PCA** for measures that have different units. If variables have different std, the result can be misleading. However, in certain settings, the variabels may be measured in the same units. In this case we might not with to scale them to have std 1. 

### Uniqueness

Each principal component loading vector is unique, up to a sign flip. So two different software package should give the same principal components, though the sign may be opposite.

### The Proportion of Variance Explained

We are interested in knowing the *proportion of variance explained* (PVE) by each principal component. The *total variance* in a data set (assuming mean zero for each variables) is defined as
$$
\sum_{j=1}^p\textrm{Var}(X_j) = \sum_{j=1}^p\frac{1}{n}\sum_{i=1}^nx_{ij}^2
$$
and the variance explained by the $m$th principal component is 
$$
\frac{1}{n}\sum_{i=1}^nz_{im}^2 = \frac{1}{n}\sum_{i=1}^n(\sum_{j=1}^p\phi_{jm}x_{ij})^2
$$
Therefore the PVE of the $m$th component is given by
$$
\frac{\sum_{i=1}^n(\sum_{j=1}^p\phi_{jm}x_{ij})^2}{ \sum_{j=1}^p\sum_{i=1}^nx_{ij}^2}
$$
In total, there are min($n-1$, $p$) principal components and their PVEs sum to one.

### Deciding How Many PC to Use

We typically are not interested in all of PC; rather we would like to use just the first few of them. We usually decide by examing a *screen plot*. Using the elbow method, we find the principal component that the subsequent variance explained drops off. 

This type of visual analysis is inherently *ad hoc*. But unfortunately, there is not well-accepted objective way to decide how many PC are enough.











