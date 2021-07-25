---
title: "Naming of Newton methods explained"
date: 2021-07-23
categories:
  - blog
tags:
  - optimization
---

Whenever people do high-order optimization, they tend to mention "the Newton method" as a Hessian-involving optimization method. Shortly after, they may refer to "Newton-Raphson", "Inflated Newton", or "Levenberg" as a Jacobian-involved solver for systems of equations. These naming-and-context variations confused me quite a bit at the beginning of my work in operations research. Eventually, I made these notes to explain to myself who is who in the Newton's zoo. 

## Root-finding method vs optimization method
When we optimize a function we're looking for critical points that satisfy the stationarity condition:

$$x^* - \text{ minimum of } f(x) \implies \nabla f(x^*) = 0$$

Because of that, the optimization can be viewed as solving a system of equations $\nabla f(x^*) = 0$. 

It works the other way around as well: one can solve a system of equations $F(x) = 0$ by minimizing $\frac{1}{2}\|F(x)\|_2^2$. Taking a gradient of it with respect to $x$ we get that $\nabla F(x) = 0$, which is the stationarity criterion above.

## Newton method or Newton-Raphson method
As a root-finding method, Newton method is defined as 

$$x_{k+1} = x_k - J^{-1}(x_k)F(x_k)$$

 where $J(x_k)$ is a Jacobian of the of the system of equations $F(x) = 0$ evaluated at $x = x_k$. 
 
 Applying this root-finding routine to the stationarity condition of an optimization problem $\min_x f(x)$  we get the Newton method from the optimization world:

$$x_{k+1} = x_k - \left[\nabla^2 f(x_k)\right]^{-1}\nabla f(x_k)$$ 

This method is guarantee to converge when the starting point $x_0$ is sufficiently close to the solution $x^*$. However, the iteration of the method is well-defined only when Hessian is non-singular at every step. Moreover, this quadratic model of $f(x)$ not have a minimizer when  Hessian is negative-definite.

## Regularized Newton method
Regularized Newton method slightly tweaks the Jacobian of the gradient (Hessian) of $f(x)$ to make it positive-definite by inflating its diagonal: 

$$x_{k+1} = x_k - (\nabla^2 f(x_k) + \lambda_k I)^{-1}\nabla f(x_k)$$

This method is also frequently referred to as a \"Newton method with diagonal inflation\".

 To ensure that $\nabla^2 f(x_k)$ is positive-definite, we need $\lambda_k \geq \lambda_{min}(\nabla^2 f(x_k))$. The naive decision would be to take a fixed super-large $\lambda_k$, but it would effectively make $(\nabla^2 f(x_k) + \lambda_k I)^{-1} \approx \lambda_k^{-1}I$, which would force us to make very small steps, slowing down computations. In practice, $\lambda_k$ is kept fixed and \"sufficiently large, but not too much\".

## Levenberg-Marquardt method

 In its simplest form, Levenberg-Marquardt method is a Newton-Raphson method where we replace an inverse of Hessian with its pseudo-inverse:

 $$\left[\nabla^2 f(x_k)\right]^{-1} \to \left[\nabla^2 f(x_k)\right]^{\dagger} = (\nabla^2 f(x_k)^T\nabla^2 f(x_k))^{-1}\nabla^2 f(x_k)^T$$
 
Notice that the matrix under the inverse at the end can still be singular. To prevent it, we use the same trick as above: we add $\lambda_k I$ term to Hessian's main diagonal. Substituting the result into Newton method's step above yields Levenberg-Marquardt algorithm:

 $$x_{k+1} = x_k - (\nabla^2 f(x_k)^T\nabla^2 f(x_k) + \lambda_k I)^{-1}\nabla^2 f(x_k)^T\nabla f(x_k)$$

