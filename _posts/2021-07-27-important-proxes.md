---
title: "Useful prox-operators and their derivations"
date: 2021-07-23
categories:
  - blog
tags:
  - optimization
---

I often use the Proximal Gradient Descent (PGD) method as my starting point for solving regularized optimization problems. This method is very simple to implement; it allows to generate a baseline that "just works" before using smarter and more complicated methods. 

Suppose you have an optimization problem 

$$\min_x f(x) + R(x)$$

where $x \in \mathbb{R}^n$, $f(x)$ is smooth and $R(x)$ is not, but it's \"simple enough\". The former normally includes the likelihood function, whereas the latter represents regularization terms.

This setup can be solved via a simple first-order iteration:

$$x^+ = \text{prox}_{\alpha R} \left[x - \alpha \nabla f(x)\right]$$

where $\alpha$ is a sufficiently small step-size.

The question is, how do we derive the proximal operator for a given $R$? Below I provide the derivations for three most common regularizers: LASSO, A-LASSO, and SCAD. Some of them, like SCAD, are particularly hard to find in literature in a form that is accessible to Ph.D. students like me. 

## Separability of Proxes

First, recall that if $R(x)$ is separable by the coordinates of $x$, then we can evaluate its proximal operator by applying its coordinate proxes element-wise:
$$
    R(x) = \sum_i r(x_i),\, x \in \mathbb{R}^n  \implies \text{prox}_{\alpha R}(z) = [\text{prox}_{\alpha r}(z_1), \dots, \text{prox}_{\alpha r}(z_n)]
$$

All regularizers in this article are separable, so we only need to derive a scalar function $\text{prox}_{\alpha r}(z)$, $z \in \mathbb{R}$, which is considerably easier task to do.

## SCAD

For a scalar variable $x \in \mathbb{R}$, SCAD-regularizer is defined as 
$$
r(x) = \begin{cases} 
    \sigma |x|, & |x| \leq \sigma \\
    \frac{-x^2 + 2\rho\sigma |x| - \sigma^2}{2(\rho - 1)}, & \sigma < |x| < \rho\sigma \\ \frac{\sigma^2(\rho + 1)}{2}, & |x| > \rho\sigma 
    \end{cases}
$$

To evaluate the $\text{prox}_{\alpha r}$ operator we need to solve the following minimization problem:

$$
    \min_{x} r(x) + \frac{1}{2\alpha}(x - z)^2
$$


To identify the set of stationary points $\{x^*\}$ of a non-smooth function $f(x)$, we use the sub-differential criterion of optimality:

$$
    0 \in \partial_x f(x^*)
$$

where $\partial_x f(x)$ denotes a sub-differential set of $f$ at the point $x$. Application of this criterion to the our minimization problem yields:

$$
    0 \in \frac{1}{\alpha}(x^* - z) + \partial r(x)_{x = x^*}
$$

Since $r(x)$ is piece-wise defined the precise value of $\partial r(x)_{x = x^{\*}}$ will depend on $x^{\*}$:

1.  Let $0 < x^{\*} \leq \sigma$, then we have $\partial r(x)_{x = x^*} = \{x^{\*}\}$ and so
   
    $$
        x = z - \sigma\alpha, \quad z \in [\sigma\alpha, \sigma + \sigma\alpha]
    $$

2. Let $-\sigma\alpha \leq x^{\*} < 0$, then we have $\partial r(x)_{x = x^{\*}} = \{-x^{\*}\}$ and so
   
    $$
        x = z + \sigma\alpha, \quad z \in [-\sigma - \sigma\alpha, -\sigma\alpha]
    $$

3. Let $x^{\*} = 0$, then $\partial r(x)_{x = x^{\*}} = [-1, 1]$, which yields
   
    $$
        \frac{1}{\alpha}(x^* - z) \in -\sigma[-1, 1]
    $$

    or

    $$
        x^{*}\in z + [-\sigma\alpha, \sigma\alpha]
    $$

    Given that $x^{\*} = 0$, it provides us with the condition on $z$:

    $$
        z \in [-\sigma\alpha, \sigma\alpha]
    $$

4. Let $\sigma < x^{\*} < \rho\sigma$, then $r(x)_{x = x^{\*}} = \frac{-{x^\*}^2 + 2\rho\sigma x^{\*} - \sigma^2}{2(\rho - 1)}$, which gives us 
   
    $$
        \frac{1}{\alpha}(x^{*} - z) = \frac{x^{*} - \rho\sigma}{\rho - 1}
    $$

    To ensure that the stationary point is indeed a minimizer, we need to ensure that 

    $$
        \frac{1}{\alpha} - \frac{1}{\rho - 1} > 0
    $$

    which implies that $\alpha < \rho - 1$. Since $\alpha$ is normally equal to the product of the regularization coefficient $\kappa$ and the step-size of PGD, it places a practical restriction on the latter. However, given that $\rho > 2$, this constraint boils down to the step-size being smaller than $1/\kappa$, which is a part of a well-known Lipschitz restriction for a step-size of Gradient Descent algorithms.
    
    Rearranging the terms to express $x^{\*}$ as a function of $z$ we get the value of prox minimizer:

    $$
        x^{*} = \frac{(\rho - 1)z - \lambda\rho\sigma}{\rho - 1 - \alpha}
    $$

    Flipping this expression back to $z$ being a function of $x^{\*}$ we get

    $$
        z = \frac{x^{*}(\rho - 1 + \lambda) + \alpha\rho\sigma}{\rho - 1} \in\left[\frac{\sigma(\rho - 1 - \alpha) + \alpha\rho\sigma}{\rho - 1}, \frac{\sigma\rho(\rho - 1 - \alpha) + \alpha\rho\sigma}{\rho - 1} \right]
    $$

    which, after algebraic simplifications, becomes

    $$
        z \in [\sigma + \alpha\sigma, \rho\sigma]
    $$

5. Let $-\rho\sigma < x^{\*} < -\sigma$, then, similarly to the previous case, we get 
   
    $$
        \frac{1}{\alpha}(x^{*} - z) = \frac{x^{*}+\rho\sigma}{\rho - 1}
    $$

    where the sign of the second term in the denominator changes due presence of absolute value. Rearranging the terms to express $x$ in terms of $z$ we get:

    $$
        x^{*} = \frac{(\rho - 1)z + \lambda\rho\sigma}{\rho - 1 - \alpha}
    $$

    Rearranging the terms to express $z$ in terms of $x^{\*}$, and using that $-\sigma\rho < x^{\*} < \sigma$ we get

    $$
        z \in [-\sigma -\alpha\sigma, -\sigma]
    $$

6. Finally, when $\|x^{\*}\| \geq \sigma\rho$, we have $\partial r(x)_{x = x^{\*}} = \{0\}$ and so 

    $$
        x^{*}= z, \quad |z| \geq \sigma\rho
    $$
    
Bundling all six cases together we get 

$$
    \text{prox}_{\alpha r}(z) = \begin{cases} sgn(z)(|z| - \sigma\alpha)_+, & |z| \leq \sigma(1+\alpha) \\ \frac{(\rho - 1)z - sgn(z)\rho \sigma\alpha}{\rho - 1 - \alpha}, & \sigma(1+\alpha) < |z| \leq \rho\sigma \\ z, & |z| > \rho\sigma \end{cases}
$$

As a sanity check, substituting $\alpha = 1$ we get a classic result by Fan et. al. (2001). 
    
## A-LASSO 

A-LASSO regularizer is defined as 

$$
    r(x) = w|x|
$$

where $w$ is normally defined as $1/\|\hat{x}\|$ where $\hat{x}$ is the parameter value from the solution of a non-regularized problem. It was introduced by Zou, et. al. (2006). For the purposes of deriving the proximal operator of A-LASSO, $w$ is a constant and positive scalar value. 

The derivation of the proximal operator of A-LASSO nearly matches the steps 1, 2, and 3 that of SCAD above. We wish to evaluate

$$
    \min_{x} w|x| + \frac{1}{2\alpha}(x - z)^2
$$

as a function of $z$.

The sub-differential optimality criterion yields

$$
    0 \in \frac{1}{\alpha}(x^{*} - z) + w\partial |x|
$$

1. Let $0 < x^{\*}$, then we have $\partial r(x)_{x = x^{\*}} = \{x^{\*}\}$ and so
   
    $$
        x^{*} = z - \alpha w, \quad z > \alpha w
    $$

2.  Let $x^{\*}< 0$, then we have $\partial r(x)_{x = x^{\*}} = \{-x^{\*}\}$ and so
   
    $$
        x^{*} = z + \alpha w, \quad z < -\alpha w
    $$

3. Let $x^{\*} = 0$, then $\partial r(x)_{x = x^{\*}} = [-1, 1]$, which yields
   
    $$
        \frac{1}{\alpha}(x^{*} - z) \in [-w, w]
    $$

    or

    $$
        x^{*} \in z + [-\alpha w, \alpha w]
    $$
    
    Given that $x^{\*} = 0$, it yields the condition on $z$:

    $$
        z \in [-\alpha w, \alpha w]
    $$


Combining all cases together we get

$$ 
    \text{prox}_{\alpha r}(z) = sgn(z)(|z| - \alpha w)_+
$$

## LASSO 
LASSO is a particular case of A-LASSO above when $w = 1$.