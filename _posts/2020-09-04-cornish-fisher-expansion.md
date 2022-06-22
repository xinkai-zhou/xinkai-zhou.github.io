---
layout: post
title: "Cornish-Fisher Expansion"
snippet: A neither-corny-nor-fishy expansion for quantiles
tags: [statistics]
---

Cornish-Fisher Expansions are used to approximate quantiles of a distribution. Its derivation is almost immediate from Edgeworth expansions.

Let $$S_n$$ be a statistic whose distribution admits the Edgeworth expansion: 

$$P(S_n \leq x) = \Phi(x) + n^{-1/2}p_1(x)\phi(x) + n^{-1}p_2(x)\phi(x) + \cdots + n^{-j/2}p_j(x)\phi(x) + \cdots. \hspace{1cm} (1)$$

See the last two posts ([1](), [2]()) for notations. Let $$w_\alpha$$ denote the $$\alpha$$-level quantile of $$S_n$$, defined as 

$$w_\alpha = \inf_x\{P(S_n\leq x) \geq \alpha\}.$$

Further let $$z_\alpha$$ be the $$\alpha$$-level quantile of a standard normal random variable, so $$\Phi(z_\alpha)  = \alpha$$.

The Edgeworth expansion above suggests an expansion of $$w_\alpha$$ in terms of $$z_\alpha$$:

$$w_\alpha = z_\alpha +  n^{-1/2}p_{11}(z_\alpha) + n^{-1}p_{21}(z_\alpha) + \cdots + n^{-j/2}p_{j1}(z_\alpha) + \cdots,$$

where $$p_{j1}$$'s are polynomials. This is called the **Cornish-Fisher Expansion**, developed by E. A. Cornish and R. A. Fisher around 1937.

To derive the form of $$p_{11}, p_{21}, ...$$, we evaluate equation (1) at $$w_\alpha$$, which gives 

$$\alpha = \Phi(w_\alpha) + n^{-1/2}p_1(w_\alpha)\phi(w_\alpha) + n^{-1}p_2(w_\alpha)\phi(w_\alpha) + \cdots.$$

Then we apply Taylor expansions on four functions: $$\Phi, \phi, p_1, p_2$$, all at $$z_\alpha$$. After simplification, we have 

$$\begin{array}{lcl} \alpha & = & \alpha + n^{-1/2}[p_{11}(z_\alpha) + p_{1}(z_\alpha)]\phi(z_\alpha) + \\ & & n^{-1}[p_{21}(z_\alpha) - \frac{1}{2}z_\alpha p_{11}(z_\alpha)^2 + p_{11}(z_\alpha)\{p'_1(z_\alpha) - z_\alpha p_{1}(z_\alpha)\} + p_{2}(z_\alpha)]\phi(z_\alpha) + O(n^{-3/2})\end{array}$$

So 

$$\begin{array}{lcl} p_{11}(x) & = & -p_1(x) \\ p_{21}(x) & = & p_1(x)p'_1(x) - \frac{1}{2}xp_1(x)^2 - p_2(x)\end{array}.$$

That concludes the derivation. For more on this subject, see Hall (2013).

#### Reference

Hall, Peter. The bootstrap and Edgeworth expansion. Springer Science & Business Media, 2013.