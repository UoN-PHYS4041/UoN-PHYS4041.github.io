(sec-tomography)=
# Bloch sphere, tomography, and optimisation

The quantum state of many qubits quickly becomes very complicated. In order to understand the main concepts in the following lectures, let us revisit the simpler setting of a single qubit. In this lecture we will visualise the qubit's state and the action of quantum gates using the Bloch sphere by introducing a method of measuring the full quantum state, called tomography. Finally, we will revisit the general rotation gate introduced in the first lecture and solve a simple optimisation problem to find the angles to rotate from the initial qubit state to the target qubit state, and visualise the optimisation process on the Bloch sphere.

## Single qubit tomography

Given a general state of a qubit in the form 

$$
|\psi\rangle = \alpha |0\rangle + \beta |1\rangle,
$$

we found in the first two lectures that measurement are determined by the probabilities $|\alpha|^2$ and $|\beta|^2$. By repeating an experiment many times we can approximate these probabilities. However, $\alpha$ and $\beta$ are complex numbers, and so $|\alpha|^2$ and $|\beta|^2$ do not fully specify the state.

When we measure, it is with respect to a particular basis. What if we measure the same state in another basis, such as the $x$-basis. This can be done by applying the Hadamard gate to the state and then measuring in standard $z$-basis. In that case, we have

$$
    H|\psi\rangle = \frac{1}{\sqrt{2}}(\alpha + \beta) |0\rangle + \frac{1}{\sqrt{2}}(\alpha - \beta) |1\rangle,
$$

and so the probabilities that determine the measurement outcomes are now $|\alpha \pm \beta|^2/2$. Therefore, if we repeat the experiment, in the $z$-basis and then the $x$-basis we have more information about $\alpha$ and $\beta$. 

With these two bases, we still don't have the full information about the state. To get the final information we also need to measure in the $y$-basis. This can be done by applying the $S^\dagger$ gate and the Hadamard before measuring in the standard basis. That is

$$
    H S^\dagger|\psi\rangle = \frac{1}{\sqrt{2}}(\alpha -i \beta) |0\rangle + \frac{1}{\sqrt{2}}(\alpha +i \beta) |1\rangle,
$$

and the corresponding probabilities are $|\alpha \mp i\beta|^2/2$. We now have all the information needed to uniquely specify the complex numbers $\alpha$ and $\beta$.

By performing measurements in three different bases we have been able to extract enough information to fully specify the quantum state of the qubit. This process of measuring the full quantum state by using different measurement bases is called tomography (or full state tomography). This can be generalised to more than one qubit but we need an exponential number of experiments in the number of qubits. Therefore, on a practical level, we do not have access to the full quantum state, but only a limited number of expectation values.

````{admonition} Exercises 3.1

**1.** Given the state $|\psi\rangle = \alpha |0\rangle + \beta |1 \rangle$, show that

$$
    H|\psi\rangle = \frac{1}{\sqrt{2}}(\alpha + \beta) |0\rangle + \frac{1}{\sqrt{2}}(\alpha - \beta) |1\rangle,
$$

and that 

$$
    H S^\dagger|\psi\rangle = \frac{1}{\sqrt{2}}(\alpha -i \beta) |0\rangle + \frac{1}{\sqrt{2}}(\alpha +i \beta) |1\rangle.
$$

**2.** Show that $\langle \psi | X | \psi \rangle = \frac{1}{2}|\alpha + \beta|^2 - \frac{1}{2}|\alpha - \beta|^2$.

**3.** Show that $\langle \psi | Y | \psi \rangle = \frac{1}{2}|\alpha -i \beta|^2 - \frac{1}{2}|\alpha +i \beta|^2$.

````

## The Bloch sphere revisited

Equipped with tomography as a method of finding the state of a qubit, let us revisit the parametrisation of the qubit state we considered in the first lecture:

$$
|\psi\rangle = \cos(\theta/2) |0\rangle + e^{i\phi} \sin(\theta/2) |1\rangle.
$$

If we measure in the $z$-basis then we get probabilities $\cos^2(\theta/2)$ and $\sin^2(\theta/2)$. We therefore don't have any information about $\phi$. Measuring the the $x$-basis we get $(1 \pm \cos(\phi) \sin(\theta))/2$. However, since $\phi\in [0,2\pi)$, this still doesn't uniquely give us $\phi$. For example, $\cos(\pi/4) = \cos(3\pi/4)$. Finally measuring in the $y$-basis we get $(1 \pm \sin(\phi) \sin(\theta))/2$. If we know both $\cos(\phi)$ and $\sin(\phi)$ then we know $\phi$ and have all of the information about the state.


Previously we said that we can interpret the angles $\theta$ and $\phi$ as parametrising the surface of a unit sphere, but didn't offer much motivation. Now we will show that this picture is indeed justified and useful. Let us now consider the values of the corresponding expectation values $\langle X \rangle$, $\langle Y \rangle$, $\langle Z \rangle$. Since we already have the probabilities in the corresponding bases we can compute these, but let's also compute these directly:

$$
\begin{aligned}
    \langle X \rangle = \langle \psi | X | \psi \rangle &= \left(\cos(\theta/2) \langle 0| + e^{-i\phi} \sin(\theta/2) \langle 1|  \right) X \left(\cos(\theta/2) |0\rangle + e^{i\phi} \sin(\theta/2) |1\rangle  \right) \\
    &= \left(\cos(\theta/2) \langle 0| + e^{-i\phi} \sin(\theta/2) \langle 1|  \right) \left(\cos(\theta/2) |1\rangle + e^{i\phi} \sin(\theta/2) |0\rangle  \right) \\
    &= e^{i\phi} \cos(\theta/2)\sin(\theta/2) \langle 0 | 0 \rangle + e^{-i\phi} \cos(\theta/2)\sin(\theta/2)) \langle 1 | 1 \rangle \\
    & = \cos(\phi)\sin(\theta),
\end{aligned}
$$

where in the third line we used that $\langle 0 | 1 \rangle = \langle 1 | 0 \rangle = 0$. We can see that this agrees with the difference of probabilities $(1 + \cos(\phi) \sin(\theta))/2 - (1 - \cos(\phi) \sin(\theta))/2 = \cos(\phi)\sin(\theta)$. Repeating with the other expectation values we can put them together in a column vector

$$
    \left(\begin{matrix} \langle X \rangle \\
                         \langle Y \rangle \\
                         \langle Z \rangle 
    \end{matrix}\right)
    =
    \left(\begin{matrix} \cos(\phi)\sin(\theta) \\
                         \sin(\phi)\sin(\theta) \\
                         \cos(\theta)
    \end{matrix}\right).
$$

This defines a three-dimensional vector, corresponding to the parametrisation of a unit sphere in 3D! This is exactly the Bloch sphere. The expectation values of the $X$, $Y$ and $Z$ operators corresponds to the three dimensional coordinates on the Bloch sphere.


Let us now consider the effects of the Pauli rotation gates on this state. Starting with the $X$ rotations $R_X(\lambda) = \cos \frac{\lambda}{2} 1 - i \sin \frac{\lambda}{2} X$. We can find its action on the 3D vector as follows

$$
    \left(\begin{matrix} \langle \psi| R_X(-\lambda) X R_X(\lambda) |\psi \rangle \\
                         \langle \psi| R_X(-\lambda) Y R_X(\lambda) |\psi \rangle \\
                         \langle \psi| R_X(-\lambda) Z R_X(\lambda) |\psi \rangle 
    \end{matrix}\right)
    =
    \left(\begin{matrix} \langle X \rangle \\
                         \langle \cos(\lambda) Y - \sin(\lambda) Z  \rangle \\
                         \langle \sin(\lambda) Y + \cos(\lambda) Z  \rangle 
    \end{matrix}\right)
    = \left(\begin{matrix} 1 & 0 & 0 \\
                            0 & \cos\lambda & -\sin\lambda \\
                            0 & \sin\lambda & \cos\lambda
    \end{matrix}\right)
    \left(\begin{matrix} \langle X \rangle \\
                         \langle Y \rangle \\
                         \langle Z \rangle 
    \end{matrix}\right).
$$

Here we used $XYZ=i1$ and $X^2 = Y^2 = Z^2 = 1$.
That is, the $R_X$ rotation gate is equivalent to rotating the 3D Bloch vector around the $x$-axis. Repeating this, we find similar for the $y$ and $z$ rotations. In {numref}`fig:bloch_rotations` we show the simulated tomography of a qubit state after the applying the three rotation gates. We indeed find that the gates rotate the state on the Bloch sphere around the three axes. The code to perform tomography and plot the Bloch sphere is included in [BlochSphere-tomography.ipynb](../downloads/BlochSphere-tomography.ipynb).


```{figure} ../images/BlochSphereRotations.pdf
---
name: fig:bloch_rotations
width: 40%
align: center
---

Pauli rotation gates on the Bloch sphere. The qubit state is measured using tomography and plotted as a 3D Bloch vector. The different rotations are shown in different colours for a range of angles.
``` 


````{admonition} Exercises 3.2

**1.** For $|\psi \rangle = \cos(\theta/2) |0\rangle + e^{i\phi} \sin(\theta/2) |1\rangle$, show that
$\langle \psi | Y | \psi \rangle = \sin(\phi) \sin(\theta)$.  

**2.** Show that $R_X(-\lambda) X R_X(\lambda) = X$.

**3.** Show that $R_X(-\lambda) Z R_X(\lambda) = \sin(\lambda) Y + \cos(\lambda) Z$.

````


## Optimising a rotation gate

In {numref}`sec-qubit` we said that a general unitary gate can be expressed as the following product of Pauli-rotation gates:

$$
    U = R_Z(\phi_1) R_Y(\phi_2) R_Z(\phi_3),
$$ (eq-general_rotation_second)

where here we have dropped the overall phase factor. However, it is not clear how the angles $\phi_i$ translate into the final rotation. Put another way, if we wanted to achieve a particular unitary gate we would have to do some work to find the angles $\phi_i$. Using the analysis from the previous section we could solve this quite simply since it boils down to a $2\times 2$ complex matrix equation (or $3\times 3$ real matrix equation). Instead, we will now use this as a simple exercise in optimising quantum circuits, which will form the basis of the subsequent lectures.

Consider the following setup:
- We prepare our qubit in some particular initial state. This can be done by applying $R_Z(\theta_1) R_Y(\theta_2)$ to the initial $|0\rangle$ state of the qubit. That is $|\psi_\text{initial} \rangle = R_Z(\theta_1) R_Y(\theta_2)|0\rangle$. For concreteness, let us take $(\theta_1, \theta_2) = (0, 0.3\pi)$. Otherwise, we could start with random angles.
- Perform tomography on this state to get the corresponding 3D Bloch vector.
- Let use choose a target state $|\psi_\text{target}\rangle$ in the form of a 3D Bloch vector. This can be done by randomly sampling elements of the 3D vector from normal distributions and then normalising the vector.
- Apply the rotation gate Eq.{eq}`eq-general_rotation_second` to our initial state. Start with $(\phi_1, \phi_2, \phi_3) = (0,0,0)$.

Our goal is now to optimise the angles so that we rotate our initial state to the target state. In quantum circuit notation, our circuit will look like the following
```{figure} ../images/RotationOptimisation.png
---
name: fig:bloch_rotations
width: 95%
align: center
---

``` 
where $A \in \{1, H, HS^\dagger\}$ is the gate needed to change to each of the three bases.

To perform the minimisation we need to define a ``loss`` or ``objective`` function. This can simply be the distance between the vectors

$$
    L(\vec{\phi}) = \sqrt{\sum_{P\in\{X,Y,Z\}} \big(\langle \psi_\text{target} |P | \psi_\text{target} \rangle - \langle \psi_\text{initial} |P | \psi_\text{initial} \rangle \big)^2}.
$$

The expectation values $\langle \psi_\text{target} |P | \psi_\text{target} \rangle$ are specified by our 3D Bloch vector for the target state. To get $\langle \psi_\text{initial} |P | \psi_\text{initial} \rangle$, which implicitly depends on the angles $\phi_i$, we need to perform tomography. Finally, we need a way to update our angles in order to minimise this loss function. We can do this using gradient descent, 

$$
    \phi_i \leftarrow \phi_i - \nabla L(\vec{\phi}).
$$

We then want to compute the gradient $\nabla L(\vec{\phi})$. There are multiple ways to do this (which we will revisit in later in the course), but the simplest would be the second-order finite difference approximation

$$
    [\nabla L(\vec{\phi})]_i \approx \frac{L(\vec{\phi}+c\vec{e}_i) - L(\vec{\phi}-c\vec{e}_i)}{2c},
$$

where $\vec{e}_i$ is a unit vector with zeros except in the i$^\text{th}$ entry. In other words, $\vec{e}_i$ perturbs $\phi_i$. With this method, we are required to perform tomography on our state seven times for each iteration. We use this second-order approximation because it is more stable when we are close to the target. Repeating the gradient descent update several times we can optimise the angles and find the required rotation gate. This is shown in {numref}`fig:rotation_optimisation`. Code performing the optimisation is provided in [RotationOptimisation.ipynb](../downloads/RotationOptimisation.ipynb).


```{figure} ../images/RotationOptimisationExample.png
---
name: fig:rotation_optimisation
width: 95%
align: center
---

Optimisation of the qubit rotation gate. Given an initial and target qubit state we perform gradient descent to find the angles required to rotate between the two. (Left) The value of the loss function during the gradient descent. (Right) Bloch sphere plot of the initial and target states as well as the path taken by the gradient descent optimisation. Blue dots correspond to the rotated state for each iteration.
``` 

````{admonition} Exercises 3.3

**1.** (Code) Modify [RotationOptimisation.ipynb](../downloads/RotationOptimisation.ipynb) so that the initial vector on the Bloch sphere is random.

**2.** (Code) Modify [RotationOptimisation.ipynb](../downloads/RotationOptimisation.ipynb) so that the Bloch sphere rotation that we are otimising is instead
$$
    U = R_X(\phi_1) R_Y(\phi_2) R_X(\phi_3).
$$
Does the optimisation still work?

**3.** (Code) Modify [RotationOptimisation.ipynb](../downloads/RotationOptimisation.ipynb) so that the Loss function is
$$
    L(\vec{\phi}) = \sum_{P\in\{X,Y,Z\}} \big(\langle \psi_\text{target} |P | \psi_\text{target} \rangle - \langle \psi_\text{initial} |P | \psi_\text{initial} \rangle \big)^2,
$$
that is, without the square root. Does the optimisation still work?


````


````{admonition} Code downloads

[BlochSphere-tomography.ipynb](../downloads/BlochSphere-tomography.ipynb) - Jupyter notebook for the tomography of a qubit state and plotting on the Bloch sphere.

[BlochSphere-tomography-IBM.ipynb](../downloads/BlochSphere-tomography-IBM.ipynb) - Jupyter notebook for the tomography of a qubit state and plotting on the Bloch sphere, which can be run on a real IBM device.

[RotationOptimisation.ipynb](../downloads/RotationOptimisation.ipynb) - Jupyter notebook with code for optimising a rotation gate.

````



---

## References
```{bibliography}
:filter: docname in docnames
```