---
layout: post
title: Parallelize without Embarrassment
snippet: What can you do with two Taylor expansions?
tags: [statistics]
---

Recall the first statistics class you took. No, not what grade you got. Instead, recall if the professor ever mentioned computer architecture? Probably not. 

Traditionally, when we talk about statistics, we talk about some probabilistic models, some parameters we want to estimate, and methods for estimating them. We have assumed that as long as we have a clever algorithm, then the computer will take care of the rest: it will have instantaneous access to any data points that the algorithm needs. This is totally safe to assume when datasets were small, but then there came the data boom and the end of Moore’s law (insert eerie music here). 

Nowadays datasets are so large that it's no longer possible to access any data points at any time using a single machine, so recent works have focused on parallel, distributed structures, and divide-and-conquer type of algorithms. Examples include (Duchi, Agarwal, and Wainwright 2012; Zhang, Duchi, and Wainwright 2013) for the frequentist setting and (Wang and Dunson 2015; Neiswanger, Wang, and Xing 2015) for the Bayesian setting.

These works use what's known as the "embarrassingly parallel" approach. The meaning of "embarrassingly parallel" depends on the setting. In the frequentist setting, one would estimate parameters on each local machine and then aggregate them on a central machine (usually by averaging). In the Bayesian setting, one would run MCMC on each local machine, and then transmit local posterior samples to a central machine to produce the global posterior.

<center><img src="../assets/parallel.png"></center>

The "embarrassingly parallel" approach is simple, but has a myriad of problems. In the frequentist setting, for example, it's difficult to construct confidence intervals using averaging estimators; Moreover, averaging can perform poorly when the estimator is nonlinear (think variance components); Furthermore, this approach works well (in the minimax sense) only when each local machine has sample size $$\Omega(\sqrt N)$$ where $$N$$ is the global sample size. This is highly restrictive because it means the number of machines one can use must be much smaller than $$\sqrt N$$.

So what's a better approach? Here's the answer given by (Jordan, Lee, and Yang 2019): <span class="highlight-blue">Communication-efficient Surrogate Likelihood (CSL)</span>. 

The problem setup is simple: 
- We have i.i.d data stored on k machines. Let $$z_{ij}, i\in\{1, ..., n\}, \text{ } j\in\{1, ..., k\}$$ denotes data point $$i$$ from machine $$j$$. Apparently, $$N = nk$$.
- $$\{P_\theta, \text{ }\theta\in\Theta\}$$ is a family of probabilistic models parameterized by $$\theta$$. The parameter space $$\Theta \subset \mathbb{R}^d$$.
- $$\mathcal{L}:\Theta\times \mathcal{Z} \mapsto \mathbb{R}$$ is a twice-differentiable loss function. You can think of it as the negative log-likelihood. From here we can define local and global empirical risk as:

$$\mathcal{L}_j(\theta)= \frac{1}{n}\sum_{i=1}^n\mathcal{L}(\theta; z_{ij}), \text{ for } j \in \{1, ..., k\}$$

$$\mathcal{L}_N(\theta)= \frac{1}{N}\sum_{i=1}^n\sum_{j=1}^k\mathcal{L}(\theta; z_{ij})= \frac{1}{k}\sum_{j=1}^k\mathcal{L}_j(\theta).$$

Now take a pause and tell me: how would you minimize $$\mathcal{L}_N(\theta)$$ with respect to $$\theta$$? Let's say you are using Newton's algorithm and don't care about computational cost. 

You may say: why not just calculate the gradient vectors and the information matrices on each machine, and then transmit them to a central machine to get the global gradient and information matrix? 

Totally. If we don't care about computational cost, then this suggestion would be perfect. 

The problem, however, is that this approach could incur extremely high communication cost. Transmitting gradients is not too bad because they are just d-dimensional vectors. But transmitting information matrices requires $$O(kd^2)$$ bits of communication per iteration, which quickly becomes prohibitive as $$d$$ becomes large. To put things in perspective, data transmission within the CPU can reach 10 GB/sec, whereas over a network it slows down by 1000 times to merely 10 MB/sec. 

<span class="highlight-blue">To analyze large and distributed data efficiently, we should no longer be satisfied with **just** getting a good estimate for $$\theta$$. The second and equally important objective is to keep the communication cost low. </span>

Here is how CSL achieved it. The beauty of CSL is that it needs nothing more than two Taylor expansions.

The first Taylor expansion is on $$\mathcal{L}_N(\theta)$$:

$$\mathcal{L}_N(\theta) = \mathcal{L}_N(\bar\theta) + \langle\nabla \mathcal{L}_N(\bar\theta), \theta - \bar{\theta}\rangle + \sum_{2}^{\infty} \frac{1}{j!}\nabla^j \mathcal{L}_N(\bar\theta) (\theta - \bar{\theta})^{\otimes j}. $$

Like I said above, transmitting higher order derivatives is expensive. So we replace them with local approximations from the first machine M1 (without loss of generality, we assume that M1 is the central machine), and define:

$$\tilde{\mathcal{L}}_N(\theta) = \mathcal{L}_N(\bar\theta) + \langle\nabla \mathcal{L}_N(\bar\theta), \theta - \bar{\theta}\rangle + \sum_{2}^{\infty} \frac{1}{j!}\nabla^j \mathcal{L}_1(\bar\theta) (\theta - \bar{\theta})^{\otimes j}.$$

To get rid of the infinite sum, we need a Taylor expansion on $$\mathcal{L}_1(\theta)$$:

$$\mathcal{L}_1(\theta) = \mathcal{L}_1(\bar\theta) + \langle\nabla \mathcal{L}_1(\bar\theta), \theta - \bar{\theta}\rangle + \sum_{2}^{\infty} \frac{1}{j!}\nabla^j \mathcal{L}_1(\bar\theta) (\theta - \bar{\theta})^{\otimes j}.$$

Now substitute the infinite sum in  $$\tilde{\mathcal{L}}_N(\theta)$$ with that in $$\mathcal{L}_1(\theta)$$: 

$$\tilde{\mathcal{L}}_N(\theta) = \mathcal{L}_N(\bar\theta) + \langle\nabla \mathcal{L}_N(\bar\theta), \theta - \bar{\theta}\rangle + \mathcal{L}_1(\theta) - \mathcal{L}_1(\bar\theta) - \langle\nabla \mathcal{L}_1(\bar\theta), \theta - \bar{\theta}\rangle $$

Finally, after removing constants that don't matter to optimization, we get $$\tilde{\mathcal{L}}_N(\theta)$$, which is called the CSL:

$$\tilde{\mathcal{L}}_N(\theta) := \mathcal{L}_1(\theta) - \langle \theta, \nabla \mathcal{L}_1(\bar\theta) - \nabla \mathcal{L}_N(\bar\theta)\rangle.$$

Given the definition, the algorithm follows naturally: 
- Initialize $$\theta^{(0)}$$ using machine M1;
- In each iteration:
    - Transmit $$\theta^{(t)}$$ to local machines;
    - On each machine, calculate the local gradient $$\nabla \mathcal{L}_j(\bar\theta)$$ and transmit it back to M1;
    - Form the CSL $$\tilde{\mathcal{L}}_N(\theta)$$ using local gradients;
    - Optimize the CSL to get $$\theta^{(t+1)}$$.

In each iteration, since we only need to transmit (1) the current $$\theta^{(t)}$$ to local machines and (2) local gradients to the central machine, the communication cost is $$O(kd)$$, which is manageable even for large $$k$$ and $$d$$.

The paper went on to discuss the theoretical properties of CSL and showed exciting simulation results. I encourage you to read the paper to see how CSL can be used for high-dimensional regularized estimation and Bayesian inference. 

To summarize, I think this paper is interesting because it provides an alternative to the widely used "embarrassingly parallel" approach for analyzing large and distributed data. But more importantly, unlike the "embarrassingly parallel" approach, CSL provides a natural way of conducting statistical inference (such as constructing confidence intervals, see their Figure 3), which is of top priority to statisticians (or anyone who cares about quantifying uncertainty).  With that said, there are issues that need further study. For example, I'm not comfortable with their theoretical result that $$\sqrt{n}X_t$$ ($$X_t = \|\theta^{(t)} - \hat{\theta}\| _2 / \|\theta^{(t-1)}  - \hat{\theta}\| _2$$, $$\theta^{(t)}$$ is the CSL estimate from iteration $$t$$, $$\hat{\theta}$$ is the estimate from $$\tilde{\mathcal{L}}_N$$) is only stochastically bounded, not convergent. This is probably why the squared estimation loss stabilized around some value above zero in their simulation results (e.g., Figure 2). In any case, this is a good start. Kudos to the authors.




### References
- Duchi, John C., Alekh Agarwal, and Martin J. Wainwright. "Dual averaging for distributed optimization: Convergence analysis and network scaling." IEEE Transactions on Automatic control 57.3 (2012): 592-606.
- Zhang, Yuchen, John C. Duchi, and Martin J. Wainwright. "Communication-efficient algorithms for statistical optimization." The Journal of Machine Learning Research 14.1 (2013): 3321-3363.
- Wang, Xiangyu, and David B. Dunson. "Parallelizing MCMC via Weierstrass sampler." arXiv preprint arXiv:1312.4605(2013).
- Neiswanger, Willie, Chong Wang, and Eric Xing. "Asymptotically exact, embarrassingly parallel MCMC." arXiv preprint arXiv:1311.4780 (2013).
- Jordan, Michael I., Jason D. Lee, and Yun Yang. "Communication-efficient distributed statistical inference." Journal of the American Statistical Association (2018).


<!---
$$||\tilde{\theta} - \hat{\theta} ||_2 = O_p(n^{-\frac{1}{2}}) ||\bar{\theta} - \hat{\theta} ||_2$$ 

Let $X_t = \frac{||\tilde{\theta}^{(t)} - \hat{\theta} ||_2 }{||\tilde{\theta}^{(t-1)}  - \hat{\theta} ||_2 }$, then $\sqrt{n}X_t$ is stochastically bounded, i.e., $\forall \epsilon >0$, $\exists \delta >0, T > 0$ s.t. $$P(\sqrt{n}X_t > \delta) < \epsilon, \text{ } \forall t > T$$

-->

