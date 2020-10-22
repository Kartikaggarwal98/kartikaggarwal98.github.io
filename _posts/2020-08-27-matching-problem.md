---
layout: post
comments: true
title: "Demontmort's Matching Problem"
date: 2020-09-27 00:00:00
tags: bayesian maths
---
> Demontmort's Matching Problem with intuitive explanation and simulation.


<!--more-->

<!--     

{: class="table-of-content"}
* TOC
{:toc} -->

Imagine the following scenario:

<em> In a country wedding in France, every person removes their hat and places it on the hook before entering the lobby. After the wedding, each guest randomly grabs up a hat and leaves without looking over. What are the chances that atleast one guest has their own hat?</em>

![gif]({{ '/assets/images/demont/hats.jpg' | relative_url }})
{: style="width: 50%;" class="center"}

This problem is similar to the **Card Matching problem**:

$ Q.1 $ In a well shuffled deck of cards labelled $ 1 $ to $ n $, what is the probability that atleast one card matches? A match is considered when the $ i^{th} $ card in the deck is numbered $ i $.

$ Q.2 $ Will the probability increase or decrease if the number of cards are increased?

The problem is the classic case of Inclusion-Exclusion principle. Before understanding about the principle, let's try to simulate this problem on a deck of 20 cards and see the experimental result.

![gif]({{ '/assets/images/demont/1.gif' | relative_url }})
<!-- {: style="width: 100%;" class="center"} -->

We see as the number of simulation increases, the probability of atleast one match converges to approx. 0.63. Hence, there is around 63% chance that atleast one card will match in the deck or in the case of original scenario: atleast one guest will have their own hat after the wedding. Now let's do some calculations to see if we can arrive at the same result.

***

# Inclusion-Exclusion Principle

Consider three events A, B, C. Can we write the probability of the union of these three sets as:

$$ P( A \cup B \cup C) = P(A) + P(B) + P(C) $$ 

Did we add too much? Yes. [Fig.1]

![all]({{ '/assets/images/demont/1.png' | relative_url }})
{: style="width: 80%;" class="center"}

The intersection of the events have been double counted so we need to subtract (**exclude**) those from the equation.

$$ P( A \cup B \cup C)= P(A) + P(B) + P(C) - P(A \cap B) - P(B \cap C) - P(C \cap A)  $$

Did we subtract too much? Yes. [Fig. 2] 

The intersection of the three events have been completely subtracted so we need to add (**include**) that to the equation. See the pattern? (include-exclude-include-....)

$$ P( A \cup B \cup C)= P(A) + P(B) + P(C) - P(A \cap B) - P(B \cap C) - P(C \cap A) + P(A \cap B \cap C)  $$

This is referred as the <em>**inclusion-exclusion principle**</em>. The generalized form of the equation can now be written as:

$$ P(A_1 \cup A_2 \ldots \cup A_n) = \sum_{j=1}^n P(A_j) - \sum_{i<j} P(A_i \cap A_j) + \sum_{i<j<k} P(A_i \cap A_j \cap A_k) - \ldots (-1)^{n+1} P(A_1 \cap A_2 \ldots \cap A_n) $$

***

Now, lets come back to our initial card matching problem.

$$ P(\text{Atleast one card matches})=  P(A_1 \cup A_2 \ldots \cup A_n)=  P(\text{One card matches}) - P(\text{Two card matches})+ \ldots - P(\text{All card match})$$

Calculating each term individually.


* $ P(A_j) $: Probability that the $ j^{th}$ card in the deck is $ j $. As all cards are equally likely to occur:

$$ P (A_j) = \frac{1}{n} $$ 

$\implies P(\text{One card matches}) = n* P(A_j) = n*\frac{1}{n} $ as there are n cards.

* $ P(A_1 \cap A_2) $: Probability that the $ 1^{st} $ and $ 2^{nd} $ card in the deck match i.e. they are labeled $ 1 $ and $ 2 $ respectively.

$$ P (A_1 \cap A_2) = \frac{(n-2)!}{n!} = \frac{1}{n(n-1)} $$

$ \implies P(\text{Two card matches}) = (\text{No. of ways to choose two cards})* P(A_1 \cap A_2) = {n \choose 2} *\frac{1}{n(n-1)} $

* Similarly, 

$$ P(A_1 \cap A_2 \cap \ldots A_k) = \frac{1}{n(n-1)\ldots(n-k+1)} $$

$$ \implies P( k \text{ card matches}) = (\text{No. of ways to choose } k \text{ cards})* P(A_1 \cap A_2 \cap \ldots A_k) = {n \choose k} *\frac{1}{n(n-1)\ldots(n-k+1)} $$

**Finally,**

$$
\begin{aligned}
P(\text{Atleast one card matches}) & = n*\frac{1}{n} -  {n \choose 2} *\frac{1}{n(n-1)} + \ldots (-1)^{n+1}{n \choose n}*\frac{1}{n!} \\

 & =  n*\frac{1}{n} -  \frac{n(n-1)}{2!} *\frac{1}{n(n-1)} + \ldots (-1)^{n+1}\frac{1}{n!} \\

& = 1 -  \frac{1}{2!} + \frac{1}{3!}\ldots (-1)^{n+1}\frac{1}{n!} \\

& = 1-\frac{1}{e} = 0.63
 \end{aligned}
 $$

Hence, there is a 63% chance that atleast one card matches. As we can see from the result, this **chance does not depend on the number of cards in the deck** contrary to our intuition that the chances of matching would change as the number increases.

## References

[1] Blitzstein, Joseph K., and Jessica Hwang. Introduction to probability. Crc Press, 2019.



