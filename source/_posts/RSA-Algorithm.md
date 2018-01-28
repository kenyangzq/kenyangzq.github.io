---
title: RSA Algorithm
date: 2018-01-27 23:26:24
categories:
- Reading Note
tags:
- Algorithm
- Blockchain
---

# RSA Algorithm

RSA (Rivest–Shamir–Adleman) is one of the first public-key cryptosystems and is widely used for secure data transmission.

## How it works

Thanks to my number theory class, I can appreciate and understand how the algorithm works. 

#### Before start

As a convention, use Alice and Bob as example to explain the algorithm. Suppose Alice and Bob want to pass some information between them. Before they start, they choose a large number N which is the product of two prime number. For a simple example, let's use 61 and 53. Then N is 3233 in this case. 

Generally people make N pretty large. The length of N in binary notation is the length of the key, and in general, people choose it to be 1024 or 2048 bits. In our case, $3233_{10} = 110010100001_{2}$, which is 12 bits. 

Now all we have is $N = 3233$.

#### Preparation

Apply the Euler function $\phi$ on N. By definition, $\phi(N)$ equals the number of positive integers less than N that are coprime with N. And there is a general formula for $\phi$
$$
\phi(N) = N\prod_{\text{p | N}}(1-\frac{1}{p})
$$
The proof is pretty simple and straightforward. Since N is just a product of two prime p and q, we have $\phi(N) = (p-1)(q-1)$. We denote $\phi(N)$ as $r$.

 Then Bob and Alice need to choose a number $e$ that's coprime with $r$. The reason behind this is to ensure a *modular multiplicative inverse* of $e$  modulo $r$ can be found. 

By definition if b is a modular multiplicative inverse of a module r if
$$
a*b \equiv 1 \; mod(r)
$$
Mathematicians have developed systematic way to find multiplicative inverse and with computer, it only needs few lines of code. 

Let's say we find the multiplicative inverse of $e$ modulo $r$ is $d$. Let's take $e=17  $ and $d=2753$ in our case. Now they have finished their preparation and are ready to pass message. 

In addition, as a spoiler, $(N, e)$ will be the public key and $(N, d)$ will be the private key.



#### Encryption

Suppose Bob has a message $M$ to pass. He first translate it into a large integer $m$ using some scheme he and alice agrees on. Then he encrypts the message by
$$
c \equiv m^e \; mod(N)
$$
And send $c$ to Alice. 



#### Decryption

After receive $c$, Alice recover the message by
$$
c^d \equiv (m^e)^d \equiv m \; mod(N)
$$
The proof is as follow:
$$
ed = k*r + 1
$$
for some integer k by the definition of modulo operation and the fact that $e$ and $d$ are multiplicative inverse of each other. 

Then 
$$
m^{ed} \equiv m*(m^{r})^k \; mod(N)
$$
Note that $r = \phi(N)$. According to Euler's Theorem:
$$
a^{\phi(N)} \equiv 1 \; mod(N)
$$
if $a$ and $N$ are coprime positive integer. This theorem is a generalization of Fermat's  Little Theorem, which is again built upon Wilson's Theorem. They can all be learned in a intro to number theory class. 

Thus we have
$$
c^d = m^{ed} \equiv m * (m^r)^k \equiv m*(1)^k \equiv m \; mod(N)
$$
based on the properties of modulo operation.

Now Alice have the information Bob sent and all she need to do is to use the translation scheme to obtain the original message.



## To Break RSA

Since the public key $(N, e)$ as it named, is public. An attacker only need to decompose the large number $N$ and obtain $\phi(N)$ to break the whole algorithm. However, such problem is well-known in mathematics as a very hard one for centuries. At least for now, no known method can guarantee to break the algorithm using fixed amount of resource. 





## Last Thought

I dig in RSA algorithm or the public-key cryptosystems since I am reading a book about block chain. Public-key cryptosystems is one of the building block in bitcoin, and I think they use SECP256k1 in particular. Other algorithms used include various kind of hashing, e.g. SHA-256, and some encoding / decoding algorithm. 







