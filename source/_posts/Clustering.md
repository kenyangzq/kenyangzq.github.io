---
title: Clustering
date: 2017-06-18 23:45:57
categories:
  - Study Note
  - Machine Learning
tags: 
  - Machine Learning
---

# Clustering

Clustering is a unsupervised learning technique to find subgroups or *clusters* in a data set. 

Comparing with PCA:

- PCA looks to find a low-dimensional representation of the observations that explain a good fraction of the variance.
- Clustering looks to find homogeneous subgroups among the observations.

The two most famous clustering techniques are *K-means clustering* and *hierarchical clustering*. In K-means, we seek to partition the observations into predefined number of clusters. In hierarchical clustering, we do not know in advance how many clusters will there be; we end up with a tree-like visual representation of the observations, called *dendrogram*.

## K-Means Clustering

It partitions the observations into k non-overlapping clusters. Need to specify $K$ first. Let $C_1,C_2,…,C_k$ be sets containing the indices of data in each cluster. Then they are exhaustive and mutually exclusive. We want the *within-cluster variance* to be as small as possible, which is denoted by $W(C_i)$.

Finding K-means clustering is the same as solving problem:
$$
minimize\{\sum_{k=1}^K W(C_k) \}
$$
By far the most common way to define $W(C_i)$ is using squared Euclidean distance, by

$$
W(C_k) = \frac{1}{|C_k|}\sum_{i,i'\in C_k}\sum_{j=1}^p(x_{ij}-x_{i'j'} )^2
$$
where $p$ is the dimension of the feature space.

Solving the above minimization problem is pretty hard. Fortunately, there is a simple algorithm that gives a pretty good solution:

— Algorithm:

1. Randomly assign a number from 1 to $K$ to each of the observations. These are the initial cluster assignments
2. Iterate until the cluster assignments stop changing:
   1. For each of the $K$ clusters, compute the cluster *centroid*. The kth cluster centroid is the vector of the $p$ feature means for the observations in that cluster.
   2. Assign each observations to the cluster whose centroid is closest in the context of Euclidean distance.

This algorithm guarantees to decrease the target sum at each step and it will finally reach a local optimum. Since the algorithm doesn't ensure a global optimum, it's important to run the algorithm with multiple times with different random initial configuration.

In addition, selecting the number $K$ is also a difficult problem.

## Hierarchical Clustering

The most common type of hierarchical clustering is *bottom-up* or *agglomerative* clustering. It means to build the dendrogram from the leaves and combining clusters up to the trunk. 

To interpret the dendrogram, just remember observations that are similar muse at the bottom and those that are quite different muse much later. Location of leaves means nothing.

To identify clusters in dendrogram, horizontally cut the tree and find the branches. Cut at different height may give different number of clusters. A dendrogram can be used to obtain any number of clusters.

— Algorithm

1. Begin with n observations and a measure (e.g. Euclidean distance) of all the n(n-1)/2 pairwise dissimilarities. Each of the n observations is treated as its own cluster.
2. Fuse the two most similar till there is only one cluster left. The dissimilarity between the most similar two indicates the height at which the fusion should be taken.

For this algorithm to work, need to extend dissimilarity to between groups of observations. Use the notion of *linkage*. 

4 types of linkage:

1. Complete: maximal intercluster dissimilarity
2. Single: minimal intercluster dissimilarity
3. Average: mean intercluster dissimilarity
4. Centroid; dissimilarity between the centroids

## Issues for Clustering

#### Small Decisions with Big Impacts

- For hierarchical clustering, 
  - What dissimilarity measure to use?
  - What type of linkage?
  - Where should we cut the dendrogram to obtain the clusters?
- For K-means clustering
  - What value of $K$?

#### Validating the Clusters

Need to validate such that our clustering is not a clustering of the noise.