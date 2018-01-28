---
title: Neural Network Note 1
date: 2017-08-24 21:34:21
tags: 
- Machine Learning
- Deep Learning
---

# Basic Neural Network

​	In last few months, I have taken 3 online courses on coursera and udemy about machine learning. Now I am going on a serious of deep learning course by <a href = "https://lazyprogrammer.me/">lazy programmer </a>. I do love this series a lot and I want to post some of my note on my blog. 

## Forward Propagation	

It's quite a work to type the derivation and theory part in latex so I choose to just scratch the surface of them. 

The first important function in the course is the *prediction* of a neural network using forward propagation. We create a 3 groups classification problem and predict the label use randomly initialized weight and bias. 

```python
import numpy as np 
import matplotlib.pyplot as plt 

Nclass = 500 

X1 = np.random.randn(Nclass,2) + np.array([0,-2])
X2 = np.random.randn(Nclass,2) + np.array([2,2])
X3 = np.random.randn(Nclass,2) + np.array([-2,2])
X = np.vstack([X1, X2, X3])

Y = np.array([0]*Nclass + [1]*Nclass + [2]*Nclass)

plt.scatter(X[:,0], X[:,1], c=Y, s = 100, alpha = .5)
# plt.show()	

D = 2
M = 3
K = 3

W1 = np.random.randn(D,M)
b1 = np.random.randn(M)
W2 = np.random.randn(M,K)
b2 = np.random.randn(K)

def forward(X,W1,b1,W2,b2):
	Z = 1 / (1 + np.exp(-X.dot(W1) - b1))
	A = Z.dot(W2) + b2
	expA = np.exp(A)
	Y = expA / expA.sum(axis = 1, keepdims = True)
	return Y

def classification_rate(Y, P):
	n_correct = 0
	n_total = 0
	for i in range(len(Y)):
		n_total +=1 
		if Y[i] == P[i]:
			n_correct += 1
	return float(n_correct) / n_total

P_Y_given_X = forward(X,W1,b1,W2,b2)
P = np.argmax(P_Y_given_X, axis=1)

assert(len(P) == len(Y))

print("Classification rate for randomly chosen weights:", classification_rate(Y,P))
```

The code is pretty straightforward.

## Back propagation

The back propagation however, need some math derivation for the formula. 

I really like the video "*The WRONG way to learn Backpropagation*" in this course. People do not understand bp by just taking the difference between target and prediction and claim that multiplying this $\Delta$ with weight is somehow the derivative of the cost function. We find this by strict math derivation and observe the pattern then build this algorithm. The key is to expose the pattern yourself using gradient decent and substitute the repeating pattern. 

#### Some Hardcore Math

Suppose we are working on a multiclass classification problem with $K$ target classes.

Let's say we have a cost function $L = \sum_n\sum_k t_k^n \log(y_k^n)$ where $n$ denotes the sampel number and $k$ is for all the class. This is the famous cross entropy function and I have also discussed it in my previous note *Beauty in Math*. 

Suppose we have $D$ input features and 1 hidden layer with $M$ nodes. We use $W_{DM}$ and $V_{MK}$ to denote the weight between input layer and hidden layer and weights between hidden layer and output layer respectively. For simplicity, suppose there is no bias terms. And further assume that we are using sigmoid function as activation function on hidden layer and softmax function on output layer. 

Now our goal is to derive $\frac{\partial L}{\partial V_{MK}}$ and $\frac{\partial L}{\partial W_{DM}}$. First apply chain rule
$$
\frac{\partial L}{\partial V_{mk}} = \sum_n \sum_{k'} t_{k'}^n \frac{1}{y_{k'}^n}\frac{\partial y_{k'}^n}{\partial V_{mk}}
$$
Note here $k'$ is not the same as $k$ and $V_{mk}$ denotes one entry in weight matrix $V$.

We know that
$$
y_{k'}^n = softmax(V_{K}^TZ) = \frac{\exp^{a_k}}{\sum_j \exp^{a_j}} \\
a_k = V_K^T Z = \sum_m V_{mk} Z_m
$$
where $Z$ denote the output at the hidden layer. 

If $k = k'$ , we have $\frac{\partial y_{k'}}{\partial a_k} = y_{k'}(1-y_k)$, and else $\frac{\partial y_{k'}}{\partial a_k} = -y_{k'}y_k$. So we can rewrite them using Kronecker Delta as
$$
\frac{\partial y_{k'}}{\partial a_k} = y_{k'}(\delta_{k'k} - y_k)
$$
Then 
$$
\frac{\partial y_{k'}^n}{\partial V_{mk}} = \frac{\partial y_{k'}^n}{\partial a_{k}} \frac{\partial a_k}{\partial V_{mk}} =y_{k'}(\delta_{k'k} - y_k) Z_m
$$
So 
$$
\begin{align}
\frac{\partial L}{\partial V_{mk}} &= \sum_n \sum_{k'} t_{k'}^n \frac{1}{y_{k'}^n}y_{k'}^n(\delta_{k'k} - y_k^n) Z_m^n \\
&= \sum_n \sum_{k'} (t_{k'}^n \delta_{k'k} - t_{k'}^ny_k^n)Z_m^n \\
&= \sum_n (t_k^n - \sum_{k'} t_{k'}^ny_k^n )Z_m^n \\
&= \sum_n (t_k^n - y_k^n )Z_m^n
\end{align}
$$
since $\sum_k t_k \equiv 1$ as each sample belongs to only one class.

The derivation for $\frac{\partial L}{\partial W_{DM}}$ is just some additional work. Since we have
$$
Z_M = \frac{1}{1 + \exp^{-W_{MK}^TX}}
$$
we can easily derive that
$$
\begin{align}
\frac{\partial L}{\partial W_{dm}} & = \sum_n (t_k^n - y_k^n ) \frac{\partial Z_m^n}{\partial W_{dm}} \\
& = \sum_n (t_k^n - y_k^n )Z_m^n (1-Z_m^n)X_d^n
\end{align}
$$
The pattern here is actually already visiable. If we have one more hidden layer with same activation function, all we need to do is just to replace $X_d^n$ with the hidden value and multiply by addition factors. Because of the recursive nature of derivatives, back propagation is able to handle any deep neural network. 

When putting these into code, we definitely want to vectorize them. It's pretty easy to make mistake as there are so many matrices to keep track of. One easy way I use to double check is to check the size of the matrix. Make sure their inner sizes match and the output size is the same as the target weight.

#### Code

In this code example, we keep working on the previous three groups problem.

```python
import numpy as np 
import matplotlib.pyplot as plt 

def forward(X,W1,b1,W2,b2):
	Z = 1 / (1 + np.exp(-X.dot(W1)-b1))
	A = Z.dot(W2) + b2
	expa = np.exp(A)
	Y = expa / expa.sum(axis = 1, keepdims = True)
	return Y, Z

def classification_rate(Y,P):
	n_correct = 0
	n_total = 0
	for i in range(len(Y)):
		n_total += 1
		if Y[i] == P[i]:
			n_correct += 1

	return float(n_correct) / n_total


def cost(T, Y):
	tot = T * np.log(Y)
	return tot.sum()

def derivative_w2(Z, T, Y):
	return Z.T.dot(T - Y)

def derivative_b2(T,Y):
	return (T-Y).sum(axis=0)


def derivative_w1(X, Z, T, Y, W2):
	dZ = (T - Y).dot(W2.T) * Z * (1 - Z)
	return X.T.dot(dZ)

def derivative_b1(T, Y, W2, Z):
	return ((T-Y).dot(W2.T) * Z * (1-Z)).sum(axis=0)

def main():
	Nclass = 500
	D = 2 # input
	M = 3 # hidden layer
	K = 3 # output

	X1 = np.random.randn(Nclass,D) + np.array([0,-2])
	X2 = np.random.randn(Nclass,D) + np.array([2,2])
	X3 = np.random.randn(Nclass,D) + np.array([-2,2])
	X = np.vstack([X1, X2, X3])

	Y = np.array([0]*Nclass + [1]*Nclass + [2] * Nclass)
	N = len(Y)

	T = np.zeros((N,K))
	for i in range(N):
		T[i,Y[i]] = 1

	# plt.scatter(X[:,0], X[:,1], c=Y, s=100, alpha=.5)
	# plt.show()

	W1 = np.random.randn(D, M)
	b1 = np.random.randn(M)
	W2 = np.random.randn(M, K)
	b2 = np.random.randn(K)

	learning_rate = 10e-7
	costs = []
	for epoch in range(100000):
		output, hidden = forward(X,W1,b1,W2,b1)
		if epoch % 100 == 0:
			c = cost(T, output)
			P = np.argmax(output, axis = 1)
			r = classification_rate(Y,P)
			print("epoch", epoch, "cost", c, "classification rate:",r)
			costs.append(c)

		W2 += learning_rate *  derivative_w2(hidden, T, output)
		b2 += learning_rate *  derivative_b2(T, output)
		W1 += learning_rate *  derivative_w1(X, hidden, T, output, W2)
		b1 += learning_rate *  derivative_b1(T, output, W2, hidden)

	plt.plot(costs)
	plt.show()


if __name__ == '__main__':
	main()
```

