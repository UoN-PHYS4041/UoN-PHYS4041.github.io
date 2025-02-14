(sec-gradient)=
# Quantum gradients and optimisation

A central part of variational quantum algorithms, including the quantum machine learning algorithms we will introduce in the final section, is the optimisation of quantum circuits with respect to some objective (or cost) function. One way to achieve this is by using gradient descent, or variations thereof. This involves changing parameters in the direction of the gradient of our objective functions. We will therefore be required to compute derivative of our circuits. This can be achieved by using approximations of the gradient (e.g. finite difference method {numref}`sec-finite-difference`), but in some cases it can also be computed exactly (e.g. parameter shift {numref}`sec-parameter-shift`). 

In all of the cases we are concerned with, the optimisation problem we are interested can be reduced to finding the minimum value of an expectation value,

$$
    F(\vec{\theta}) = \langle \psi(\vec{\theta}) | \hat{O} | \psi(\vec{\theta}) \rangle,
$$

where $\hat{O}$ is some observable of interest, such as the Hamiltonian $\hat{O} = \hat{H}$, considered in {numref}`sec-VQE` for the VQE, and where $|\psi(\theta)\rangle$ is a state constructed by a quantum circuit with variable parameters $\vec{\theta}$. That is, the goal is to find

$$
    F_0 = \min_{\vec{\theta}} F(\vec{\theta}) \quad \text{or} \quad \vec{\theta}_0 = \text{argmin}_{\vec{\theta}} F(\vec{\theta}).
$$

One way to achieve this is using gradient descent, which in its simplest form updates the parameters via the iterative process,

$$
    \vec{\theta}_{i+1} = \vec{\theta}_i - \eta \nabla F(\vec{\theta}_i),
$$

where the idea is that $\lim_{i\rightarrow \infty} \vec{\theta}_i = \vec{\theta}_0$. While this cannot be guaranteed, due to the unknown structure of the optimisation landscape, which may include many local minima, this is a popular approach, and reflects the established use of (variants of) gradient descent in machine learning.

(sec-finite-difference)=
## Finite difference revisited

One of the most straightforward ways of approximating the gradient of our objective / loss function $F(\vec{\theta})$ is to use the finite difference method. This is motivated by the definition of the gradient,

$$
    [\nabla F(\vec{\theta})]_i = \lim_{\delta \rightarrow 0} \frac{F(\vec{\theta}+\delta \vec{e}_i) - F(\vec{\theta})}{\delta},
$$

where $\hat{e}_i$ is a unit vector with $1$ in the $i^\text{th}$ row and all other values $0$. In practice, we are not able to compute the gradient, or this limit exactly. Instead we can use a small but finite value of $\delta$ to get the approximation,

$$
    [\nabla F(\vec{\theta})]_i \approx \frac{F(\vec{\theta}+\delta \vec{e}_i) - F(\vec{\theta})}{\delta}.
$$ (eq:finite_difference)

To understand the finite difference formula better, let us consider the Taylor series for $F(\vec{\theta} + \delta \vec{e}_i)$, which is

$$
    F(\vec{\theta} + \delta \vec{e}_i) = F(\vec{\theta}) + \delta \frac{\partial F(\vec{\theta})}{\partial \theta_i}  + \frac{\delta^2}{2} \frac{\partial^2 F(\vec{\theta})}{\partial \theta_i^2}  + \frac{\delta^3}{3!} \frac{\partial^3 F(\vec{\theta})}{\partial \theta_i^3}  + \mathcal{O} (\delta^4).
$$

Using this, we can see that Eq.{eq}`eq:finite_difference` approximates the gradient with error $\mathcal{O}(\delta)$. Using this Taylor series we can also derive higher-order approximations such as the symmetric difference formula,

$$
    [\nabla F(\vec{\theta})]_i = \frac{F(\vec{\theta}+\delta \vec{e}_i) - F(\vec{\theta}-\delta \vec{e}_i)}{2\delta} + \mathcal{O}(\delta^2).
$$

However, it is worth noting that this increased accuracy comes at the cost of twice as many function evaluations, and so in practice the lowest order approximation is typically used. It is also possible to access higher order derivatives (such as the Hessian matrix) using this method, which could be useful in more sophisticated optimisation algorithms. 

In {numref}`fig:gradient_comparison` we show the results of gradient descent using first and second order finite difference approximation for the rotation optimisation ({numref}`sec-tomography`) and VQE ({numref}`sec-VQE`). We additionally show results of the two other methods in the following sections.


```{figure} ../images/GradientComparison.png
---
name: fig:gradient_comparison
width: 100%
align: center
---

Comparison of the performance of gradient descent using different methods to approximate (or evaluate) the gradient. (Left) Optimisation of the single qubit gate from {numref}`sec-tomography`. (Right) VQE from {numref}`sec-VQE`. 
``` 


````{admonition} Exercises 5.1

**1.** Using the Taylor series

$$
    F(\vec{\theta} + \delta \vec{e}_i) = F(\vec{\theta}) + \delta \frac{\partial F(\vec{\theta})}{\partial \theta_i}  + \frac{\delta^2}{2} \frac{\partial^2 F(\vec{\theta})}{\partial \theta_i^2}  + \frac{\delta^3}{3!} \frac{\partial^3 F(\vec{\theta})}{\partial \theta_i^3}  + \mathcal{O} (\delta^4),
$$

show that the approximation

$$
    [\nabla F(\vec{\theta})]_i \approx \frac{F(\vec{\theta}+\delta \vec{e}_i) - F(\vec{\theta})}{\delta}.
$$

has error of order $\mathcal{O}(\delta)$, and that

$$
    [\nabla F(\vec{\theta})]_i \approx \frac{F(\vec{\theta}+\delta \vec{e}_i) - F(\vec{\theta}-\delta \vec{e}_i)}{2\delta},
$$

has error of order $\mathcal{O}(\delta^2)$.

````


(sec-SPSA)=
## Simultaneous perturbation stochastic approximation

The finite difference method requires use to evaluate our objective function at a number of points that scales linearly with the number of parameters in our functions. Since each evaluation corresponds to a new experiment, this can be costly if our function has many parameters. Instead we might want to sacrifice accuracy in our gradient in order perform many more steps of gradient descent. This is achieved by the method of simultaneous perturbation stochastic perturbation (SPSA).

SPSA works similarly uses the same iterative process as graident descent, namely

$$
\vec{\theta}_{n+1} = \vec{\theta}_n - a_n g(\vec{\theta}_n),
$$

but where $g(\vec{\theta}_n) \approx \nabla F(\vec{\theta}_n)$ is our approximation of the gradient. SPSA approximates this gradient in the following way,

$$
[g(\vec{\theta}_n)]_i = \frac{F(\vec{\theta}_n + c_n \vec{\Delta}_n) - F(\vec{\theta}_n - c_n \vec{\Delta}_n)}{2c_n [\Delta_n]_i},
$$

where $c_n$ is a scalar that depends on the iteration step $n$, and $\Delta_n$ is a random vector with the same length as $\vec{\theta}_n$. Rather than perturbing each of the angles separately to compute the gradient, we perturb all of the angles at the same time by a random vector $\vec{\Delta}_n$. 


```{figure} ../images/SPSA.png
---
name: fig:SPSA
width: 100%
align: center
---

Results of running SPSA 10 times. (Left) Optimisation of the single qubit gate from {numref}`sec-tomography`. (Right) VQE from {numref}`sec-VQE`. 
``` 


For this approximation to converge we need $a_n$, $c_n$ and $\vec{\Delta}_n$ to satisfy several properties. 
1. $a_n \rightarrow 0$ as $n \rightarrow \infty$ and $\sum_n a_n = \infty$,
2. $c_n \rightarrow 0$, such that $\sum_n (\frac{a_n}{c_n})^2 < \infty$,
3. $[\vec{\Delta}_n]_i$ are mutually independent random variables with zero mean, symmetrically distributed about zero, with finite inverse first and second moments.

While there are many choices that would satisfy these conditions, we can take $a_n = a/n$, with $a>0$, $c_n = c/\sqrt[3]{n}$, with $c>0$, and $[\vec{\Delta}_n]_i$ sampled from the binomial distribution $\{-1,+1\}$, with equal probability, $0.5$.

Note that SPSA is a stochastic algorithm which relies on the generation of a random perturbation vector. Because of this we should expect the gradient descent to have significant fluctuations, as seen in {numref}`fig:gradient_comparison`. Furthermore, it means the results will be different each time we run the algorithm. In {numref}`fig:SPSA` we show the results of 10 runs starting from the same initial angles and with the same target. Each individual run is different, and there is quite a spread in the rates of convergence. However, this stochastic nature of the algorithm is compensated by the drastic reduction in the number of measurements on the quantum computer that are needed. For each approximation to the gradient, we only need to run the circuit twice, in contrast to twice the number of parameters as for finite difference.


(sec-parameter-shift)=
## Parameter shift

So far we have been considering different methods for approximating the gradient of a quantum circuit in order to perform gradient descent. However, by using knowledge about the structure of the quantum circuit we are able to compute gradients exactly using the parameter shift method. This is similar to the property for sine and cosine, namely

$$
\frac{\text{d}}{\text{d} x} \sin(x) = \frac{1}{2} \left[\sin\left(x+\frac{\pi}{2}\right) - \sin\left(x-\frac{\pi}{2}\right) \right].
$$

More specifically let us consider a function that we want to optimise of the form

$$
F(\vec{\theta}) = \langle \psi(\vec{\theta}) | \hat{O} | \psi(\vec{\theta}) \rangle,
$$

where $\hat{O}$ is an observable, and $|\psi(\vec{\theta})\rangle$ is the quantum state realised by a parametrised quantum circuit. If the parameters in $|\psi(\vec{\theta})\rangle$ only appear in Pauli-rotation gates, e.g. $R_P(\theta) = \exp(-i\theta/2 P)$ (which we can always do), then we have

$$
[\nabla F(\theta)]_i = \frac{1}{2} \left[ F \left(\vec{\theta} + \frac{\pi}{2} \vec{e}_i \right) - F \left(\vec{\theta} + \frac{\pi}{2} \vec{e}_i \right) \right].
$$ (eq:parameter_shift_final)

While this looks very similar to finite difference, this gives the exact value of the gradient.

In {numref}`fig:gradient_comparison` we show the results of using the exact gradient. For these two simple problems, there appears to be little difference to the second-order finite difference method. However, there are cases where the finite difference method becomes numerically unstable, and the parameter shift would be preferred. In any case, it requires the same number of jobs to be run on the quantum computer and so would generally be preferred if our circuit if of the correct form.


### Derivation

To derive this result, let us first compute the derivative of the Pauli-Rotation gates $R_P(\theta) = \exp(-i\theta/2 P)$, where $P\in \{X,Y,Z \}$. Then we have

$$
\frac{\text{d}}{\text{d} \theta} R_P(\theta) = -\frac{i}{2} P R_P(\theta) = -\frac{i}{2} R_P(\theta) P.
$$

Let us first consider the simpler setting of a single parameter $\theta$, i.e. 

$$
f(\theta) = \langle \psi | R^\dagger_P(\theta) \hat{O} R_P(\theta) |\psi\rangle,
$$

where $|\psi\rangle$ could correspond to a quantum circuit without variable parameters. Then

$$
\begin{aligned}
\frac{\partial f}{\partial \theta} &= \langle \psi | \frac{\text{d} R^\dagger_P(\theta)}{\text{d} \theta} \hat{O} R_P(\theta) |\psi \rangle + \langle \psi | R^\dagger_P(\theta) \hat{O} \frac{\text{d} R_P(\theta)}{\text{d} \theta} |\psi \rangle \\
&= \frac{i}{2} \left[ \langle \psi | R^\dagger_P(\theta) P \hat{O} R_P(\theta) |\psi\rangle - \langle \psi | R^\dagger_P(\theta) \hat{O} P R_P(\theta) |\psi\rangle    \right] \\
& = \frac{i}{2} \left[ \langle \psi | R^\dagger_P(\theta) [P, \hat{O}] R_P(\theta) |\psi\rangle  \right],
\end{aligned}
$$

where $[A,B] = AB - BA$ is the commutator. We can then write this commutator as 

$$
[P, \hat{O}] = -i \left[R^\dagger_P \left(\frac{\pi}{2}\right) \hat{O} R_P\left(\frac{\pi}{2}\right) - R^\dagger_P\left(-\frac{\pi}{2}\right) \hat{O} R_P\left(-\frac{\pi}{2}\right)  \right],
$$

which simply follows from $R_P(\pm\pi/2) = (1 \mp iP)/\sqrt{2}$, and $R^\dagger_P(\theta) = R_P(-\theta)$. Plugging this back in we get

$$
\begin{aligned}
\frac{\partial f}{\partial \theta} &= \frac{1}{2} \Big[ \langle \psi | R^\dagger_P\left(\theta\right)R^\dagger_P\left(\frac{\pi}{2}\right) \hat{O} R_P\left(\frac{\pi}{2}\right)R_P\left(\theta\right) | \psi \rangle \\ & \qquad\quad - \langle \psi | R^\dagger_P\left(\theta\right)R^\dagger_P\left(-\frac{\pi}{2}\right) \hat{O} R_P\left(-\frac{\pi}{2}\right)R_P\left(\theta\right) | \psi \rangle  \Big] \\
& = \frac{1}{2} \Big[  \langle \psi | R^\dagger_P\left(\theta+\frac{\pi}{2}\right) \hat{O} R_P\left(\theta + \frac{\pi}{2}\right) | \psi \rangle - \langle \psi | R^\dagger_P\left(\theta-\frac{\pi}{2}\right) \hat{O} R_P\left(\theta - \frac{\pi}{2}\right) | \psi \rangle \Big]\\
&= \frac{1}{2} \left[ f\left(\theta + \frac{\pi}{2} \right) - f\left(\theta - \frac{\pi}{2} \right) \right].
\end{aligned}
$$

While above we considered a single angle $\theta$, this generalises to any number of Pauli rotation gates in our circuit. Since the partial derivatives fixes all other angles they are not considered variables for that derivative. Therefore any gates that appear before the one of interest can be incorporated into the state $|\psi\rangle$, and any gates in the circuit after can be incorporated into the operator $\hat{O}$. This can then be applied for derivatives with respect to each of the angles separately. In the end, we have that for a general function $f(\vec{\theta})$ of this form, depending on many angles only in Pauli rotation gates, then we get the result in Eq.{eq}`eq:parameter_shift_final`.


````{admonition} Exercises 5.2

**1.** For the commutator $[P, \hat{O}] = P\hat{O} - \hat{O}P$, show that

$$
[P, \hat{O}] = -i \left[R^\dagger_P \left(\frac{\pi}{2}\right) \hat{O} R_P\left(\frac{\pi}{2}\right) - R^\dagger_P\left(-\frac{\pi}{2}\right) \hat{O} R_P\left(-\frac{\pi}{2}\right)  \right],
$$

with $P=X$, using $R_X(\theta) = \cos(\theta/2) - i \sin(\theta/2) X$. 

**2.** (Code). Replace the finite difference approximation of the derivative in [VQE-simulator-simplified.ipynb](../downloads/VQE-simulator-simplified.ipynb) with the simultaneous perturbation stochastic approximation and/or the parameter shift exact derivative. How does this change the learning and the final accuracy.

````