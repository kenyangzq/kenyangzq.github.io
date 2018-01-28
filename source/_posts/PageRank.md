---
title: PageRank
date: 2017-05-26 16:20:25
categories:
	- Study Note
	- Bigdata
	- Others
tags:
	- Bigdata
	- Algorithm
---

# PageRank: Bring Order to Web

<br>

　　PageRank algorithm is the fundamental algorithm for early search engines. In my Big Data course, I also gave a 30-minute presentation on this algorithm. My slide can be found <a href="../others/PageRank.pptx" style="color:blue">here</a>. In this note, I will give a brief introduction of the famous PageRank algorithm and extend to TF-IDF, where we reach a relatively thorough way to rank Web pages.

## PageRank

​	First let's dive into the golden block of early Google, Page Rank.


### Motivation

　　Since 1999, the graph of WWW (World Wide Web) has about 150 millions nodes (pages) and around 1.7 billion edges (links). Every Web page includes some links that link to other pages, and also there are links that link to this page in other page. To make it simple, we call all the links in the a Web page that points to others *forward link* and those in other pages that point to this one the *backward link*. It's obvious that given a Web page, we can easily find out all its forward links but it's hard to know all its backward links in the entire WWW structure. 


　　In addition, we also need some other simple definition to explain PageRank. Let $u$ be a web page, define the following terms,

1. $F_u$ is the forward set of $u$, which consists of all the pages that $u$ points to.
2. $B_u$ is the backward set of $u$, which consists of all the pages that point to $u$.
3. $N_u$ is the cardinality of $F_u$, which indicates the number of pages that $u$ points to.


<img src="/images/PageRank/ABC.png" width="150" align=center>

　　For example, in the system above, there are only 3 pages and arrows indicate links that connect the pages..  $F_A = \{B,C\}$, $B_A =\{C\}$ and $N_A= 2$.



### Simple PageRank

　　Suppose we have a web page $u$, its PageRank $R(u)$ can be computed by

$$
R(u) = c \sum\limits_{v\,\in\, B_u}\frac{R(v)}{N_v}
$$

　　Before I explain the formula, I need to point out that this is the first version of PageRank in the paper; to address more clearly, I added the word *simple*.

　　The idea here is pretty straightforward. The rank of a page depends on the contribution from its backward page. Notice that this is a recursive definition.

#### Example

<img src="/images/PageRank/example.png" width="150" align = center style="display:inline">



　　Let me walk through a example to better explain the equation. So in the system above, we have 4 pages. If the PageRank algorithm converges finally, we can assign almost any value (except the eigenvector of the transition matrix) to the pages at the beginning. Here we mimic a uniform distribution. In addition, we set $c$ in the formula to 1. 

　　According to our definition, in the first iteration of the algorithm, 
$$
R(A) = \frac{R(B)}{2} + \frac{R(C)}{1} + \frac{R(D)}{3} = 0.458
$$
　　Those denominator in the fractions are $N_B$, $N_C$ and $N_D$ which we can eaily obtain by counting the arrows. Similarly, we can calculate 


$$
\begin{split}
R(B) = \frac{R(D}{3} = 0.083  \\\\    
R(C) = \frac{R(B)}{2} + \frac{R(D}{3} = 0.25  \\\\  
R(D) = \frac{R(A)}{1} = 0.25
\end{split}
$$

　　Note that we don't use the newly calculated page rank inside an iteration. 

　　Another way to look at the example is to define a transition matrix $T$, where $T_{ij}$ denotes the portion of rank flowing from page $j$ to page $i$.
　　
$$
T = 
		\left( 
		\begin{matrix}
		0 & .5 & 1 & .33 \\
		0 & 0 & 0 & 0 \\
		0 & .5 & 0 & .33 \\
		1 & 0 & 0 & 0 
		\end{matrix}
		\right)
$$

Then the new rank vector can be calculated by 
$$
R=cTR
$$

#### Problem: Rank Sink
　　There are reasons why we call this one simple PageRank and not adapt it. One problem is Rank Sink. 

<img src="/images/PageRank/sink.png" width="250">

　　Consider the above 3 page inside a web system where they form a loop. As we iterate, the rank of these pages will neve decrease but absorb all the rest PageRanks in the system. We call this problem *rank sink*



### Formal PageRank

　　To overcome rank sink, people at Google introduce a vector $E(u)$, called rank source. In the paper, they formally define page rank $R'$ as 
$$
R'(u) = c \sum\limits_{v\in B_u}\frac{R'(v)}{N_v} + cE(u)
$$
where c is maximized and the sum of all the ranks is 1.	

　　Rank source $E$ is a vector over the whole Web to make up for rank sinks. Since nowadays people rather use *damping factor*, I will not discuss it in details here. How it works is explained in Larry Page's paper. 

　　One thing to notice, one can customize the algorithm by choosing different $E$. For example, choose $E$ to be unifrom over all web page corresponds to a random surfer that may jump to random page in each iteration.



### Damping Factor

　　However, the formal PageRank is still not the one people are using now. Later version has a more clever way to deal with problems like rank sink, *damping factor*. Damping factor $d$ is just a number between 0 and 1. The new recipe is 	
$$
R(u) = \frac{1-d}{N} + d \sum\limits_{v\in B_u} \frac{R(v)}{N_v}
$$
　　Damping factor $d$ replace the role of $c$ to adjust the final sum and $N$ here is the total number of pages in the whole system. 

　　Think of the PageRank algorithm as a model of *random surfer*. In each iteration, with probability $d$, the surfer may choose a random link from the current page to go to; on the other hand, with probability $1-d$, the surfer gets bored and jumps to a random page in the system.

　　Therefore, the first term in the equation represent the probability of a surfer jumps to the current page and the second term corresponds to the probability that the surfer visits the page following a backward link. These two terms adds to the total probability that a surfer reaches a particular page at any time. Thus, the ranks of all the page form a probability distribution.

　　There is a nice demonstration in my <a href="../others/PageRank.pptx" style="color:blue">slide</a>. Feel free to download and go over it.

　　In reallity, one usually choose the damping factor to be 0.85. This is a perfect balance point between high rate of convergence and numeric stability. 



## TF-IDF

　　Another popular method to rank documents is *Term Frequency - Inverse Document Frequency* or *TF-IDF*. TF-IDF is the product of two numbers, term frequency and inverse document frequency. 

　　Term frequency, as its name, is the frequency of a given word $t$ in document $f$, aka, $tf(t,f)=$ # $t$ in file $f$.  

　　Inverse document frequency depends on the portion of documents that contain the target word among all the documents. For a given word $t$ and a collection of documents $F$, $idf(t,F) = log$(# files in $F$ / # files in $F$ that contains $t$ )

　　Then $tf\text{-}idf(t,f,F) = tf(t,f) \times idf(t,F)$.	

### Example

　　For example, given target word "Vanderbilt" and a bunch of files $f_1$, $f_2$, $f_3$ … $f_N$, the tf and idf of file $f_i$ are

　　tf("Vanderbilt", $f_i$) = # "Vanderbilt" in $f_i$

　　idf("Vanderbilt", {$f_i$}) = $log(\frac{N}{number\;of\;files\;contain\;"Vanderbilt"})$

And the $tf\text{-}idf$ of $f_i$ is the product of the above terms.



## Comparison

　　Acute reader may have notice the difference between these two methods. PageRank only focuses on the link structure in the web system and ignore whatever content a page may contain. Conversely, $tf\text{-}idf$ completely depends on the content and neglect the linkage. 

　　Therefore, people have try to combine the two methods with a weight parameter 
$$
Rank = w*PageRank + tf\text{-}idf
$$

## Conclusion

　　PageRank is an iterative link analysis algorithm to calculate relative importance of pages. The most popular version is the one that use damping factor:
$$
R(u) = \frac{1-d}{N} + d \sum\limits_{v\in B_u} \frac{R(v)}{N_v}
$$
　　TF-IDF is another useful method to rank pages. It makes up for the drawback of PageRank and therefore are often used together with PageRank.



---
## Reference 
[1]. *The PageRank Citation Ranking: Bring Order to the Web*, Larry Page, January 29, 1998.







