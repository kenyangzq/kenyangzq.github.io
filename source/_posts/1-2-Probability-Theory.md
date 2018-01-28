---
title: 1.2 Probability Theory
date: 2017-06-01 21:13:19
categories:
- Study Note
- PRML
tags: 
- Math

---

# Probability Theory

　　Recently, I study the reading of *Pattern Recognition and Machine Learning*, aka PRML. Here I record some of the key concept and idea in Chapter 1.2 Probability Theory.

　　In this note, I skip the part where the book introduces the basic idea of probability, the two fundamental rules — sum rule and product rule, and the basic idea of Bayes' theorem. The only thing I need to point out is the concept of *prior probability* and *posterior probability*. The former is the probability distribution of some event to happen *before* gather any evidence. The latter is the one for *after* some evidence is obtained.



## Expectations and Covariances

　　The expectation as we all learn in high school, is defined as 

$$
E[f] = \sum\limits_{x}p(x)\,f(x)
$$
for discrete distribution or 
$$
E[f] = \int p(x)\,f(x)
$$
for continuous variable. It's the average value of some function f(x) under probability distribution p(x). In either case, we can approximate it by finite number $N$ of points as
$$
E[f] \approx \frac{1}{N} \sum_{n=1}^{N} f(x_n)
$$
The approximation becomes exact as $N$ goes to infinity.

　　A conditional expectation with respect to a conditional distribution is defined as
$$
E_x[f(|y)] = \sum_xp(x|y)\,f(x)
$$
　　The variance of f(x) is defined by
$$
\begin{split}
var[f] &= E[(f(x) - E[f(x)])^2]  \\\\
	&= E[f(x)^2] - E[f(x)]^2
\end{split}
$$
　　For two random variables x and y, the *covariance* is defined as
$$
cov[x,y] = E_{x,y}[\{x-E[x]\}\{y-E[y]\}]
$$
In the case of two vectors of random variables $\textbf{x}$ and $\textbf{y}$, the covariance is a matrix
$$
\begin{split}
cov[\textbf{x},\textbf{y}] &= E_{\textbf{x},\textbf{y}}[\{\textbf{x}-E[\textbf{x}]\}\{\textbf{y}-E[\textbf{y}]\}] \\\\
&= E_{\textbf{x},\textbf{y}}[\textbf{x}\textbf{y}^T]-E[\textbf{x}]E[\textbf{y}^T]
\end{split}
$$

## Baysian Probability 

　　In early education, it's normal to treat probabilities in terms of the frequencies of random, repeatable events. This is called the *classical* or *frequentist* interpretation of probabilities. For some uncertain events, like whether the moon will crash toward the Earth, it's impossible to measure the frequency. However, we can use Bayesian interpretation of probability to represent uncertainty. It has been proven that such measure of uncertainty follow different sets of properties and axioms. In each case, they behave precisely according to the rules of probabilities. Thus it's natural to refer them as (Bayesian) probabilities.

　　With the observed evidence, Bayes' theorem allows as to convert a prior probability to a posterior probability. To quantify the uncertainty for the model parameter $\textbf{w}$ or even the model itself, we can use machinery of probability theory to measure. 

　　Before we observe the data, we can capture our assumptions about $\textbf{w}$ by prior probability $p(\textbf{w})$. After we have observed $\it{D} = \{t_1,…,t_N\}$, Bayes' theorem, which takes the form of
$$
p(\textbf{w}|\mathit{D}) = \frac{p(\mathit{D}|\textbf{w})p(\textbf{w})}{p(\mathit{D})}
$$
allows to evaluate the uncertainty of $\textbf{w}$ in the form of posterior probability $p(\textbf{w}|\mathit{D})$.

　　The quantity $p(\textbf{w}|\mathit{D})$ can be viewed as a function of the parameter vector $\textbf{w}$ and is called the *likelihood function*. It expresses how probable the observed data set is for different settings of the parameter vector $\textbf{w}$. With this definition, we can state Bayes' theorem in another form
$$
posterior \propto likelihood \times prior
$$
where all of these quantities are viewed as function of $\textbf{w}$. 

​　　In both the Bayesian and frequentist paradigms, the likelihood function $p(\textbf{w}|\mathit{D})$ plays a central role but the manner is different. In a frequentist setting, $\textbf{w}$ is considered as a fixed parameter, whose value is determined by some 'estimator', and error bars of this estimate are obtained by the data set $D$. 

　　One widely used frequentist estimator is *maximum likelihood*, in which $\textbf{w}$ is chosen to maximize the likelihood function. In machine learning literature, the negative log of the likelihood function is called an *error function*. Since negative log is monotonically decreasing, maximizing the function is equivalent to minimizing the error.



## The Gaussian distribution

　　The famus *normal* or *Gaussian distribution* for a single-valued variable x is defined by 
$$
N(x|\mu,\sigma^2) = \frac{1}{(2\pi\sigma^2)^{1/2}}e^{-\frac{1}{2\sigma^2}(x-\mu)^2}
$$
which is govened by two parameters, *mean* $\mu$ and *variance* $\sigma^2$. The reciprocal of the variance, written as $\beta=1/\sigma^2$, is called the precision. It's clear that Gaussian distribution is nonnegative and straightforward to show that the it's normalized. Thus it satisfies the two requirements for a valid probability density. We can easily find expectations and variance of x, which are $\mu$ and $\sigma^2$ respectively. 　　

　　For $D$-dimensional vector $\textbf{x}$ of continuous variables, the Gaussian distribution is given by
$$
N(\pmb{x}|\pmb{\mu},\pmb{\Sigma}) = \frac{1}{(2\pi)^2|\pmb{\Sigma}|^{1/2}}e^{-\frac{1}{2}(\pmb{x}-\pmb{\mu})^T\pmb{\Sigma}^{-1}(\pmb{x}-\pmb{\mu})}
$$
　　Suppose we have a data set of $N$ observations $(x_1,…,x_N)^T$ drawn independently from a Guassian distribution with mean $\mu$ and variance $\sigma^2$. We call such data set *independent and identically distributed* or *i.i.d*. We can write the probability of such data set in the form
$$
p(\mathbf{x}|\mu,\sigma^2) = \prod_{n=1}^\mathbf{N} N(x_n|\mu,\sigma^2)
$$
　　Now we can determine the unknown parameters $\mu$ and $\sigma^2$ by maximizing the likelihood function above. In practice, it's more convenient to maximize the log of the function which is
$$
\ln p(\mathbf{x}|\mu,\sigma^2)=-\frac{1}{2\sigma^2}\sum_{n=1}^{N}(x_n -\mu)-\frac{N}{2}\ln \sigma^2 - \frac{N}{2}\ln (2\pi)
$$
Maximizing it with respect to $\mu$, we obtain the maximum likelihood solution given by
$$
\mu_{ML} = \frac{1}{N}\sum_{n=1}^Nx_n
$$
which is the *sample mean*. Similarly, maximizing it with respect to $\sigma^2​$, we obtain the maximum likelihood solution for the variance in the form
$$
\sigma_{ML}^2 = \frac{1}{N}\sum_{n=1}^{N}(x_n-\mu_{ML})^2
$$
which is the *sample variance* w.r.t the sampel mean. 

　　In fact, the maximum likelihood approach systematically underestimates the variance of the distribution. This is an example of a phenomenon called *bias* and is related to the problem of over-fitting. First note that the maximum likelihood solutions $\mu_{ML}$ and $\sigma^2_{ML}$ are functions of the data set values $x_1,…x_N$. The expectations of these quantities w.r.t to the data value can be shown to be 
$$
\begin{split}
E[\mu_{ML}] &=  \frac{1}{N}E[\sum_{n=1}^N x_n] =\mu \\\\
E[\sigma^2_{ML}] &= \frac{1}{N} E[\sum_{n=1}^{N} (x_n-\mu_{ML})^2] \\\\
		&= \frac{1}{N} E[\sum_{n=1}^Nx_n^2 -2N\mu\mu_{ML} + N\mu_{ML}^2]  \\\\ 
		&= \frac{1}{N} E[\sum_{n=1}^Nx_n^2 - N\mu^2+ N(\mu-\mu_{ML})^2] \\\\ 
		&= \frac{1}{N} \{N(E[x^2] - E[x]^2) + N \frac{1}{N}\sigma^2 \} \\\\
		&= \frac{N-1}{N} \sigma^2
\end{split}
$$
So on average, the maximum likelihood estimate will obtain the correct mean but will underestimate the true variance by a factor of $(N-1)/N$. Therefore, we use
$$
\tilde{\sigma}^2 = \frac{N}{N-1} \sigma^2_{ML} = \frac{1}{N-1}\sum_{n=1}^{N}(x_n-\mu_{ML})^2
$$
to obtain an unbiased estimate of variance. Not that the bias of the maximum likelihood solution become less significant as the number N goes to infinity. 