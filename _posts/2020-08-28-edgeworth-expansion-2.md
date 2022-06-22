---
layout: post
title: "Edgeworth Expansion II"
snippet: For general statistics
tags: [statistics]
---

Last time we derived the Edgeworth expansion for the distribution of the sum of independent random variables. This time we look at more general statistics. 

Let $$S_n$$ be a statistic whose limiting distribution is $$N(0,1)$$. For example, $$S_n = n^{1/2}(\hat\theta - \theta_0)/\sigma$$. The characteristic function of $$S_n$$ is 

$$\begin{array}{lcl} \chi_n(t) & = & \mathbb{E}(\exp(itS_n)) \\ & = & \exp[\log\mathbb{E}\exp(itS_n)] \\ & = & \exp[k_{1,n}it + \frac{1}{2!}k_{2,n}(it)^2 + \cdots + \frac{1}{j!}k_{j,n}(it)^j + \cdots] \end{array}$$

where the last equality follows from the definition of cumulant, and $$k_{j,n}$$ is the $$j$$-th cumulant of $$S_n$$. Expanding $$k_{j,n}$$ as a power series in $n^{-1}$, we have 

$$k_{j,n} = n^{-(j-2)/2}(k_{j,1} + n^{-1}k_{j,2} + n^{-2}k_{j,3} + \cdots). \hspace{1cm} (1)$$

The proof of this result in the general case is rather complex. Section 2.4 of Hall (2013) gives a proof when $$S_n$$ is a smooth function of vector means. One thing to note is that since $$k_{1,n} = \mathbb E(S_n)$$ converges to 0 and $$k_{2,n} = \text{Var}(S_n)$$ converges to 1, we must have $$k_{1,1} = 0$$ and $$k_{2,1} = 1$$. Substituting $$k_{j,n}$$ into $$\chi_n(t)$$, we get 

$$\chi_n(t) = \exp[-\frac{1}{2}t^2 + n^{-1/2}\{k_{1,2}it + \frac{1}{3!}k_{3,1}(it)^3\} + n^{-1}\{\frac{1}{2!}k_{2,2}(it)^2 + \frac{1}{4!}k_{4,1}(it)^4\} + \cdots].$$

After applying the Maclaurin expansion and simplifying the result as we did in the last post, we get 

$$\begin{array}{lcl} \chi_n(t) & = & \exp(-t^2/2) [1 + n^{-1/2}\{k_{1,2}it + \frac{1}{6}k_{3,1}(it)^3\} + n^{-1}\{\frac{1}{2}k_{2,2}(it)^2 + \frac{1}{4!}k_{4,1}(it)^4 + \frac{1}{2}(k_{1,2}it + \frac{1}{6}k_{3,1}(it)^3)^2\} + \cdots] \\ & = & \exp(-t^2/2) + n^{-1/2}r_1(it)\exp(-t^2/2) + n^{-1}r_2(it)\exp(-t^2/2) + \cdots + n^{-j/2}r_j(it)\exp(-t^2/2) + \cdots \end{array}$$

Finally, applying the Fourier inversion and the Hermite polynomial results as before, we obtain 

$$P(S_n \leq x) = \Phi(x) + n^{-1/2}p_1(x)\phi(x) + n^{-1}p_2(x)\phi(x) + \cdots + n^{-j/2}p_j(x)\phi(x) + \cdots,$$

where 

$$\begin{array}{lcl} p_1(x) & = & -[k_{1,2} + \frac{1}{6}k_{3,1}(x^2 - 1)] \\ p_2(x) & = & -x[\frac{1}{2}(k_{2,2} + k_{1,2}^2) + \frac{1}{24}(k_{4,1} + 4k_{1,2}k_{3,1})(x^2-3) + \frac{1}{72}k_{3,1}^2(x^4 - 10x^2 + 15)] \end{array}$$

To derive $$k_{1,2}, k_{2,2}, k_{3,1}$$ and $$k_{4,1}$$, we use the following result from the last post: 

$$\begin{array}{lcl} k_{1,n} & = & \mathbb E(S_n) \\ k_{2,n} & = & \mathbb E(S_n - \mathbb E(S_n))^2 \\ k_{3,n} & = & \mathbb E(S_n - \mathbb E(S_n))^3 \\ k_{4,n} & = & \mathbb E(S_n - \mathbb E(S_n))^4 - 3[\text{Var}(S_n)]^2 \end{array}$$

Given a specific statistic $$S_n$$, we can calculate these quantities. Then by matching the coefficients of $$n^{-(j-2)/2}$$ with those of equation (1), we will have the explicit forms of $$k_{1,2}, k_{2,2}, k_{3,1}, k_{4,1}$$, and thus of $$p_1(x)$$ and $$p_2(x)$$.

That concludes the derivation. For more on this subject, see Hall (2013).

#### Reference

Hall, Peter. The bootstrap and Edgeworth expansion. Springer Science & Business Media, 2013.