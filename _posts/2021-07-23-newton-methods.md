---
title: "Naming of Newton methods explained"
date: 2021-07-23
categories:
  - blog
tags:
  - optimization
---

Whenever people do high-order optimization, they tend to mention "Newton method" as a Hessian-involving optimization method. Shortly after, they may refer to "Newton-Raphson", "Inflated Newton", or "Levenberg" as a Jacobian-involved solver for systems of equations. These naming-and-context variations confused me quite a bit at the beginning of my work in operations research. Eventually, I made these notes to explain to myself who is who in the Newton's zoo. 

## Root-finding method vs optimization method
When we optimize a function we're looking for critical points that satisfy the stationarity condition:

$$x^* - \text{ minimum of } f(x) \implies \nabla f(x^*) = 0$$

Because of that, the optimization can be viewed as solving a system of equations $\nabla f(x^*) = 0$ and taking the solutions that correspond to the minima. 

We can solve a system of equations $F(x) = 0$ by minimizing $\frac{1}{2}\|F(x)\|_2^2 = \frac{1}{2} F(x)^TF(x)$. The problem of minimizing $\frac{1}{2}\|F(x)\|_2^2$ is known as minimization of the sum of squares. 

Notice that However, it's a very *convenient* choice as we'll see below.

## Newton method
Newton method is an *optimization method* that attempts to minimize the quadratic approximation of the smooth function $f(x)$. Using Taylor series we get

$$f(x_{k} + \Delta x) = f(x_{k}) + \nabla f(x_k)^T\Delta x + \Delta x^T\nabla^2f(x)\Delta x + o(\Delta x^2)$$

Throwing away $o(\Delta x^2)$ leaves us the *local quadratic approximation* of $f(x)$ at $x = x_k$. To find the minimum of this approximation we differentiate it with respect to $\Delta x$ and equate it to 0, which gives us 

$$\nabla^2 f(x_k)\Delta x = -(\nabla f(x_{k}))^T$$

Notice that we need $\nabla^2 f(x)$ to be positive-definite to guarantee that $\Delta x$ both exists and minimizes the quadratic model. To guarantee this, Newton method should start from the point that is sufficiently close to the minimum, otherwise the method can diverge.

## Regularized Newton method

Regularized Newton method extends the applicability of Newton method by inflating the diagonal of Hessian of $f(x)$ to make it positive-definite: 

$$\left(\nabla^2 f(x_k) + \lambda_k I\right)(\Delta x) = -\nabla f(x_k)$$

 To ensure that $\nabla^2 f(x_k)$ is positive-definite, we need $\lambda_k \geq \lambda_{min}(\nabla^2 f(x_k))$. The naive decision would be to take a fixed super-large $\lambda_k$, but it would effectively make $(\nabla^2 f(x_k) + \lambda_k I)^{-1} \approx \lambda_k^{-1}I$, which would force us to make very small steps, slowing down computations. In practice, $\lambda_k$ is kept fixed and \"sufficiently large, but not too much\".

## Gauss-Newton method

Gauss-Newton method is a *system of equations solver*. It appears when we attempt to solve a system of equations $F(x) = 0$ via minimization of the sum of squares with the Newton method above:

$$\min_x\frac{1}{2} \|F(x)\|_2^2$$ 

Why sum of squares? It's *convenient*. Apparently the second norm is not our only option: we could find the same solutions by, say, minimizing $\|F(x)\|_1$ or even $\max_i|F(x)|_i$. However, choosing the least squares we choose a convenient *structure* of the loss, that we will later exploit.

To see how, let's calculate $\nabla\frac{1}{2} \|F(x)\|_2^2$ and $\nabla^2 \frac{1}{2}\|F(x)\|_2^2$ that we need for Newton Method:

$$\nabla \frac{1}{2} \|F(x)\|_2^2 = F(x)^T(J_F(x))$$
$$\nabla^2\left( \frac{1}{2} \|F(x)\|_2^2\right) = \nabla\left (F(x)^T(J_F(x))\right) = J_F(x)^TJ_F(x) + F(x)^TH_F(x)$$

where $J_F(x)$ is the Jacobian of $F$, and $H_F(x)$ is a 3D-tensor of the second derivatives of the equations of $F$ with respect to the components of $x$. The latter part is scary, right? Newton-Gauss does precisely what you want: it throws this part away and treats the first part as the *approximation* of Hessian:

$$\nabla^2\left( \frac{1}{2} \|F(x)\|_2^2\right)\approx J_F(x)^TJ_F(x)$$
(There are formal justifications behind this move, but we won't touch them here.)

Now, the iteration of Newton method for this system looks like:

$$J_F(x)^TJ_F(x)\Delta x = -J_F(x)^TF(x)$$

Notice that this iteration looks for a pseudo-inverse of $J_F(x)$:
$$\Delta x = -J_F(x)^{\dag}F(x)$$

But what do people mean when they refer to Gauss-Newton as an optimization method? To see this, notice that the general Newton step above may not exist when $\nabla^2f(x)$ is singular, i.e. positive-semi-definite, but not necessarily positive-definite. Substituting $F(x) = \nabla f(x)$ and $J_F(x) = \nabla^2 f(x)$ to Gauss-Newton iteration we get:

$$(\nabla^2 f(x)^T\nabla^2 f(x))\Delta x = -\nabla^2 f(x)^T\nabla f(x)$$

Now this system *always* has a solution, because the pseudo-inverse always exists. It extends the applicability of Newton-method to the zones where Hessian is positive-semi-definite while still keeping it hyperparameter-free.

## Levenberg-Marquardt method

Levenberg-Marquardt, can viewed as an adaptation of Regularized Newton approach to solving systems of equations:

$$(J_F(x)^TJ_F(x) + \lambda I)\Delta x = -J_F(x)^TF(x)$$

Now, again, instead of taking the pseudo-inverse we inflate the diagonal to make the matrix of interest invertible *everywhere* at a price of managing a hyper-parameter.