---
layout: post
title: "Edgeworth Expansion I"
snippet: For the sum of independent variables
tags: [statistics]
---

Edgeworth expansion is an important technique in statistics. It allows us to go beyond the central limit theorem (CLT) to obtain a more accurate approximation of the distribution function. One famous application is in proving the higher-order correctness of the Bootstrap method.  

The problem is, the derivation of Edgeworth expansions is rather long, so when I first read it, I quickly got lost in the notations. I hope this note would make it easier to read. 

Without further due, let's welcome $$X_1, X_2, ...$$, which are independent and identically distributed random variables with mean $$\mu$$ and variance $$\sigma^2$$. We estimate $$\mu$$ by the sample mean $$\hat{\mu} = \frac{\sum x_i}{n}$$. By the CLT, $$S_n = \sqrt n\frac{\hat{\mu} - \mu}{\sigma}$$ converges in distribution to $$N(0,1)$$.

Let's be clear about our goal. The Edgeworth expansion of the distribution of $$S_n$$ is: 

$$P(S_n \leq x) = \Phi(x) + n^{-1/2}p_1(x)\phi(x) + n^{-1}p_2(x)\phi(x) + \cdots + n^{-j/2}p_j(x)\phi(x) + \cdots ,$$

where $$\Phi(x)$$ is the cumulative distribution function (CDF) of the standard normal random variable and $$\phi(x)$$ is its density. We need to figure out how to obtain it.

## Step 1

The characteristic function of $$S_n$$ is 

$$\begin{array}{lcl} \chi_n(t) & = & \mathbb{E}(\exp(itS_n)) \\ & = & \mathbb{E}(\exp(\frac{it}{\sqrt n}\frac{X_1-\mu}{\sigma}) \cdots \exp(\frac{it}{\sqrt n}\frac{X_n-\mu}{\sigma})) \\ & = & [\chi(\frac{t}{\sqrt n})]^n\end{array}$$

where $$\chi$$ is the characteristic function of $$Y = \frac{X_1-\mu}{\sigma}$$.

By the definition of [cumulant](https://en.wikipedia.org/wiki/Cumulant), 

$$\chi(t) = \exp(k_1 it + \frac{1}{2}k_2 (it)^2 + \cdots + \frac{1}{j!}k_j(it)^j + \cdots).$$

Moreover, since characteristic function generates raw moments, we also have 

$$\chi(t) = 1 + \mathbb{E}(Y)it + \frac{1}{2}\mathbb{E}(Y^2) (it)^2 + \cdots + \frac{1}{j!}\mathbb{E}(Y^j)(it)^j + \cdots.$$

Thus 

$$\sum \frac{1}{j!}k_j(it)^j = \log(1+\frac{1}{j!}\mathbb{E}(Y^j)(it)^j) = \sum_{k\geq 1}(-1)^{k+1}\frac{1}{k}[\sum \frac{1}{j!}\mathbb{E}(Y^j)(it)^j]^k,$$

where the second equality used the Maclaurin expansion on $$\log(1+x)$$. By equating the coefficients of $$(it)^j$$, we have

$$\begin{array}{lcl} k_1 & = & \mathbb E(Y) \\ k_2 & = & \mathbb E(Y - \mathbb E(Y))^2 \\ k_3 & = & \mathbb E(Y - \mathbb E(Y))^3 \\ k_4 & = & \mathbb E(Y - \mathbb E(Y))^4 - 3(\text{var}Y)^2 \end{array}$$

Since $$Y$$ was standardized to have $$\mathbb E (Y)=0$$ and $$\text{var} (Y)=1$$, we have 

$$\begin{array}{lcl} \chi_n(t) & = & [\chi(\frac{t}{\sqrt n})]^n \\ & = & \exp[-\frac{1}{2}t^2 + n^{-1/2}\frac{1}{3!}k_3(it)^3 + \cdots + n^{-(j-2)/2}\frac{1}{j!}k_j(it)^j + \cdots] \\ & = & \exp(-\frac{1}{2}t^2) \times \exp[n^{-1/2}\frac{1}{3!}k_3(it)^3] \times \cdots \times \exp[n^{-(j-2)/2}\frac{1}{j!}k_j(it)^j] \times \cdots \end{array}$$

Using the Maclaurin expansion again, we have 

$$\begin{array}{lcl} \exp[n^{-1/2}\frac{1}{3!}k_3(it)^3] & = & 1 + n^{-1/2}\frac{1}{3!}k_3(it)^3 + n^{-1}\frac{1}{72}(k_3)^2(it)^6 + \cdots\\ \exp[n^{-1}\frac{1}{4!}k_4(it)^4] & = & 1 + n^{-1}\frac{1}{4!}k_4(it)^4 + \cdots \end{array}$$

So  

$$\chi_n(t) = \exp(-\frac{1}{2}t^2)(1 + n^{-1/2}r_1(it) + n^{-1}r_2(it) + \cdots + n^{-j/2}r_j(it) + \cdots) \hspace{1cm} (1)$$

where 

$$\begin{array}{lcl} r_1(u) & = & \frac{1}{6}k_3 u^3\\ r_2(u) & = & \frac{1}{24}k_4 u^4 + \frac{1}{72}(k_3)^2 u^6 \end{array}$$


## Step 2
Since our goal is an expansion of the distribution function, we need to use the inverse Fourier transform on $$\chi_n(t)$$.

Rewrite equation (1) as 

$$\chi_n(t) = e^{-\frac{t^2}{2}} + n^{-1/2}r_1(it)e^{-\frac{t^2}{2}} + n^{-1}r_2(it)e^{-\frac{t^2}{2}} + \cdots + n^{-j/2}r_j(it)e^{-\frac{t^2}{2}} + \cdots  \hspace{1cm} (2)$$

Since 

$$\begin{array}{lcl} \chi_n(t) & = & \int_{-\infty}^\infty e^{itx}dP(S_n\leq x) \\ e^{-\frac{t^2}{2}} & = & \int_{-\infty}^\infty e^{itx}d\Phi(x) \end{array}$$

If we further let $$R_j(x)$$ denote the function such that 

$$r_j(it)e^{-\frac{t^2}{2}} = \int_{-\infty}^\infty e^{itx}dR_j(x), \hspace{1cm} (3)$$

then applying the inverse Fourier transform on equation (2) gives 

$$P(S_n \leq x) = \Phi(x) + n^{-1/2}R_1(x) + n^{-1}R_2(x) + \cdots + n^{-j/2}R_j(x) + \cdots. \hspace{1cm} (4)$$

The inversion holds if $$X$$ has sufficiently many finite moments and a smooth distribution. See Hall (2013) for details.

We are almost done with the derivation. 

## Step 3

Finally, we need to figure out the form of $$R_j(x)$$. Applying integration by parts repeatedly, we have 

$$ e^{-\frac{t^2}{2}} = (-it)^j\int_{-\infty}^\infty e^{itx}d\Phi^{(j)}(x),$$ 

where $$\Phi^{(j)}(x) = D^j(x)$$ and $$D$$ is the differential operator $$d/dx$$. Rearranging the terms, we get 

$$ (it)^j e^{-\frac{t^2}{2}} = \int_{-\infty}^\infty e^{itx}d\{(-D)^j\Phi(x)\}.$$ 

In general, if $$r_j$$ is a polynomial, then $$r_j(-D)$$ is a polynomial of the differential operator, and thus is itself a differential operator. So we have 

$$ r_j(it) e^{-\frac{t^2}{2}} = \int_{-\infty}^\infty e^{itx}d\{r_j(-D)\Phi(x)\}.$$ 

Comparing this to equation (3), we see that 

$$R_j(x) = r_j(-D)\Phi(x).$$

By the definition of [Hermite polynomials](https://en.wikipedia.org/wiki/Hermite_polynomials), for $$j\geq 1$$ we have 

$$(-D)^j\Phi(x) = -He_{j-1}(x)\phi(x).$$

So 

$$\begin{array}{lcl} R_1(x) & = & r_1(-D)\Phi(x) \\ & = & \frac{1}{6}k_3 (-D)^3 \Phi(x) \\ & = & - \frac{1}{6}k_3 He_{2}(x)\phi(x) \\ & = & - \frac{1}{6}k_3 (x^2-1)\phi(x)\end{array}$$

Similarly we have 

$$R_2(x) = -x[\frac{1}{24}k_4(x^2-3) + \frac{1}{72}(k_3)^2(x^4-10x^2+15)]\phi(x).$$

In general, for $$j\geq 1$$, 

$$R_j(x) = p_j(x)\phi(x).$$

This together with equation (4) gives the Edgeworth expansion 

$$P(S_n \leq x) = \Phi(x) + n^{-1/2}p_1(x)\phi(x) + n^{-1}p_2(x)\phi(x) + \cdots + n^{-j/2}p_j(x)\phi(x) + \cdots.$$

The form of $$p_1$$ and $$p_2$$ are clear from $$R_1$$ and $$R_1$$, so we do not repeat.

---

That concludes the derivation. Essentially, it involves characteristic functions and Taylor expansion (step 1), inverse Fourier transform (step 2) and Hermite polynomials (step 3). For more on this subject, Hall (2013) is a good reference. 

#### Reference

Hall, Peter. The bootstrap and Edgeworth expansion. Springer Science & Business Media, 2013.




















