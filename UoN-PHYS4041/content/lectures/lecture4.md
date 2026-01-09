(sec-VQE)=
# Variational quantum eigensolver (VQE)

One of the most prominent and practical algorithms for a quantum computer is inspired by Feynman's original vision. The algorithm (or set of algorithm's) is called the variational quantum eigensolver (VQE). This is an example of a quantum-classical hybrid optimisation algorithm. The basic idea is to have an ansatz or guess on the quantum computer which has parameters that we can optimise. The measurement outcomes are fed into a classical algorithm which then proposes the next update for the quantum computer. In this case the goal is to find the lowest eigenvalue of an operator of interest, which corresponds to the ground state energy of a physical problem.


## The time-independent Schrödinger equation

Many problems in quantum many-body physics can be boiled down the solving the Schrödinger equation. Here we will be interested in the *time-independent* Schrödinger equation

$$
    \hat{H} |\psi\rangle = E |\psi\rangle,
$$(eq:Schrodinger_equation)

which is an eigenproblem.
The Hamiltonian operator $\hat{H}$ is a Hermitian operator ($H^\dagger$ = $H$) that plays the role of energy in quantum mechanical systems. The eigenvalue $E$ is the energy of a particular eigenstate that solves this equation. This is generically a hard problem! A typical approach is to approximate the Hamiltonian by a finite but large matrix, the size of which generically grows exponentially with the number of particles involved. This is the same problem we have already encountered with the size of the vector space for our qubits.

As an example, many magnetic materials can be approximated by lattices of spin-1/2 degrees of freedom, which are two level systems similar to our qubits. If we map this lattice of spins to our qubits then we can see that the size of the matrices grows exponentially with the number of sites in the lattice. The current limit for generic systems on the most powerful supercomputers is around 50 qubits / spins. It is clear that this is far from a realistic model of real materials. It is worth noting that in many cases we can do far better than this by taking advantage of symmetries, low dimensionality, semi-classical models etc. However, these approach are ineffective for many physically relevant models and here quantum computers may be able to help.

The VQE algorithm was first demonstrated in the context of quantum chemistry by finding the ground state of simple molecules {cite}`Peruzzo2014`{cite}`Kandala2017`. This is the case we will also focus on. 


## Finding the ground state

The task that we will consider is to find the ground state of the Schr{\"o}dinger equation. That is to find the state $|\psi\rangle$ that solves the Schr{\"o}dinger equation~\eqref{eq: Schrodinger equation} with the lowest energy $E$. Formally, the goal is to find the state $|\psi\rangle$ that minimises

$$
E = \min_{|\psi\rangle} \frac{\langle \psi | \hat{H} |\psi\rangle}{\langle \psi | \psi \rangle} = \min_{|\psi\rangle}\langle \psi | \hat{H} |\psi\rangle,
$$

where in the second equality we used the fact that the state $|\psi\rangle$ is normalised. On the quantum computer we always start with the zero state $|000\cdots \rangle$, and so we rephrase the problem as finding the quantum circuit $V$ such that $|\psi\rangle = V |000\cdots\rangle$, i.e., 

```{figure} ../images/VQEGeneralCircuit.png
---
name: fig:VQE_general_circuit
width: 30%
align: center
---
```

minimises the energy.

The quantum circuit in {numref}`fig:VQE_general_circuit` contains a large multiple qubit gate. As we mentioned in a previous section, we are able to approximate a general multi-qubit gate using only the single qubit rotations and the CNOT gate. The art of VQE is to come up with the best quantum circuit to approximate $V$.

The basic setup of VQE consists of the following three steps:

1. Create an ansatz quantum circuit with variable parameters that can be optimised.
2.  Measure the quantum circuit to extract the value of the energy.
3. Use a classical optimisation algorithm to propose updated parameters for the quantum circuit ansatz, then repeat from 1.

We will discuss these three steps in greater detail in the following three sections.


## Quantum state ansatz

The first step in the VQE algorithm is choose an ansatz quantum circuit which will approximate our ground state $|\psi\rangle$. This is part of the art of VQE, designing a circuit structure that can well approximate our target state. On the simplest level, we want to have enough parameters and generate enough entanglement to represent the state while using gates that are easy for us to implement, such as rotations and CNOT gates.

```{figure} ../images/VQEParameterizedCircuit.png
---
name: fig:VQE_parameterized_circuit
width: 40%
align: center
---
```

where the $U = U(\alpha, \beta, \gamma, \delta)$ gates are general single rotations of the form in Eq.{eq}`eq:general_rotation`, with different angles for each gate. The Block indicated by the dashed box can be repeated $d$ times to create an ansatz with more parameters and angles.


## Measuring the energy

The next step is to measure the energy. This is the function that we are minimising in our optimisation procedure. The energy is the expectation value of the Hamiltonian operator with respect to our ansatz state, that is,

$$
    E(\vec{\theta}) = \langle H \rangle_{\vec{\theta}} = \langle \psi | H |\psi\rangle,
$$

where $|\psi\rangle$ is our ansatz quantum state which implicitly depends on the set of angles $\vec{\theta}$.

The general form of a multi-qubit Hamiltonian is the following

$$
    H = \sum_{A=1,X,Y,Z} \sum_i h_i^A A_i + \sum_{A,B=X,Y,Z}\sum_{ij} h_{ij}^{AB} A_i B_j + \cdots,
$$

where the remaining terms contain higher order Pauli strings (products of non-trivial Pauli-operators). In practice we will often only be interested in Hamiltonians with a limited number of non-zero terms. The energy is then given by

$$
    \langle H \rangle = \sum_{A=1,X,Y,Z} \sum_i h_i^A \langle A_i \rangle + \sum_{A,B=X,Y,Z}\sum_{ij} h_{ij}^{AB} \langle A_i B_j \rangle + \cdots,
$$

where the angled brackets correspond to expectation values with respect to the ansatz state, i.e., $\langle \cdot \rangle = \langle \psi | \cdot |\psi \rangle$. Given the Hamiltonian parameters $\{h_i^A, h_{ij}^{AB},\cdots\}$, our goal is then to measure expectation values for the required Pauli strings.

Let us consider a couple of simple examples which will make it clear how to measure general Pauli strings. For concreteness, suppose we have four qubits and we want to measure $\langle Z_2 Z_3 \rangle$. Since the Pauli-Z is already diagonal in the computational basis, the corresponding circuit is

```{figure} ../images/ZBasisMeasurement.png
---
name: fig:Z_basis_measurement
width: 35%
align: center
---
```

where we only measure the second and third qubits. Note that in practice we can measure all qubits and reproduce probabilities as explained in Eq.{eq}`eq:reduced_measurement`, and so can construct several expectation values from a single set of measurements. From these measurements we can estimate the probabilities for each of the four basis states for qubits 2 and 3. The expectation value is then given by

$$
    \langle Z_2 Z_3 \rangle = p(|00\rangle) + p(|11\rangle) - p(|01\rangle) - p(|10\rangle),
$$

where the signs are given by the eigenvalues of $Z_2 Z_3$ for the given basis state, e.g., $Z_2 Z_3 |00\rangle = |00\rangle$, but $Z_2 Z_3 |01\rangle = -|01\rangle$.

As a second example, consider measuring $\langle X_2 Y_3 \rangle$. In this case the operator is not diagonal in the computational basis and so we must first make a change of basis. The corresponding circuit would be

```{figure} ../images/SecondPauliExample.png
---
name: fig:second_Pauli_example
width: 55%
align: center
---

Basis transformation for measuring the expectation value $\langle X_2 Y_3 \rangle$.
```

where the $H$ and $S$ change basis. With the extra basis change we are effectively measuring $\langle Z_2 Z_3 \rangle$ again but now for a different circuit including the basis change. That is, the expectation value is given by

$$
    \langle X_2 Y_3 \rangle = p(|00\rangle) + p(|11\rangle) - p(|01\rangle) - p(|10\rangle),
$$
where the probabilities are given by circuit in {numref}`fig:second_Pauli_example`.


## Using gradient descent

Now that we have measured the energy, we want to update our set of parameters $\vec{\theta}$ in order to lower the energy. In the simplest case we could use basic gradient descent, i.e.,

$$
    \vec{\theta}_\text{new} = \vec{\theta}_\text{old} - \eta \nabla E(\vec{\theta}_\text{old}),
$$

where $\eta$ is the learning rate. We then want to compute the gradient of the energy $\nabla E(\vec{\theta}_\text{old})$. There are multiple ways to do this (which we will revisit in the next lecture), but the simplest would be the finite difference approximation

$$
    [\nabla E(\vec{\theta})]_i \approx \frac{E(\vec{\theta}+c\vec{e}_i) - E(\vec{\theta}-c\vec{e}_i)}{2c},
$$

where $\vec{e}_i$ is a unit vector with zeros except in the i$^\text{th}$ entry. In other words, $\vec{e}_i$ perturbs $\theta_i$. With this method, the number of times we are required to measure the energy is twice the number of parameters $\vec{\theta}$.

There are many other ways we could compute the gradient that require fewer measurements of the energy such as stochastic approximation methods. Furthermore, we can use more sophisticated optimisation techniques than simple gradient descent such as the ADAM optimiser. However, for our purpose, this conceptually simple method will suffice.

Once we have updated the parameters we can start the loop again, measure the energy, update the parameters and repeat until the energy converges.

(sec:explicit_example)=
## Explicit example

To clarify the above discussion of VQE, let us consider an explicit example. The example we consider is the one used when the VQE was first introduced. We want to find the ground state energy of the H-He$^+$ molecule. This problame was solved using VQE in Ref.{cite}`Peruzzo2014`. The approach we will take is closer to the more recent work Ref.{cite}`Kandala2017`.

We will find the ground state of the molecule at a fixed separation, $R$, where it has the following two-qubit effective Hamiltonian

$$
    H = \frac{1}{2} \left[ J_x (X1 + 1X) + J_z (Z1 + 1Z) + J_{xx}XX + J_{zz}ZZ + J_{xz}(XZ + ZX) + C  \right],
$$

where we omit kronecker products and use short hand, e.g., $X1 = X\otimes 1$. How to find the form of this Hamiltonian or the values of the parameters is far beyond this course. This Hamiltonian was found using PSI3 software using the Born-Oppenheimmer approximation {cite}`Peruzzo2014`. To be concrete, for $R=90$pm, the parameters are 
$J_x = -0.2288, 
    J_z = -1.0466,
    J_{xx} = 0.2613,
    J_{zz} = 0.2356,
    J_{xz} = 0.2288,
    C = -3.8505.
$

For our VQE algorithm, the first step is to decide on our quantum state ansatz. In this case we will use

```{figure} ../images/VQEAnsatz.png
---
name: fig:VQE_ansatz
width: 75%
align: center
---
```

where we have introduce a new two-qubit gate, called the CZ or controlled-Z gate. The CZ gate is diagonal in the computational basis, and has trivial action except $\text{CZ}|11\rangle = -|11\rangle$. The matrix representation for this gate is

$$
    \text{CZ} = \begin{pmatrix} 1 & 0 & 0 & 0 \\ 0 & 1 & 0 & 0 \\ 0 & 0 & 1 & 0 \\ 0 & 0 & 0 & -1 \end{pmatrix}.
$$

The CZ gate is simply related to the CNOT gate by the following

```{figure} ../images/CZGate.png
---
name: fig:CZ_gate
width: 40%
align: center
---
```
This gate is symmetric with respect to the two qubits, and was chosen to simply the form of our ansatz circuit. Our ansatz circuit then contains eight angles $\theta_i$ that we want to optimise the get the lowest energy.

In order the evaluate the energy, i.e., the expectation value of the Hamiltonian, we need to measure expectation values of the form

$$
    \langle X \rangle, \quad \langle Z \rangle, \quad \langle XX \rangle, \quad \langle ZZ \rangle, \quad \langle XZ \rangle.
$$

In order to extract these we need to rotate into four different bases, namely,

```{figure} ../images/ExampleBasisTransformations.png
---
name: fig:expr_basis_transformations
width: 95%
align: center
---
```

where $U(\vec{\theta})$ is our quantum circuit ansatz. The first circuit allows us to measure $\langle Z 1 \rangle$, $\langle 1Z \rangle$ and $\langle ZZ \rangle$, the second circuit $\langle ZX \rangle$, the third circuit give $\langle XZ \rangle$, and the final circuit $\langle X1 \rangle$, $\langle 1 X \rangle$ and $\langle XX \rangle$. From four sets of measurements we are able to extract the expectation values of all eight of the required Pauli strings.

The final part is the classical optimisation. In the simplest case we can use gradient descent with the finite difference method, i.e., 

$$
    [\nabla E(\vec{\theta})]_i = \frac{E(\vec{\theta} + c\vec{e}_i) - E(\vec{\theta} - c\vec{e}_i)}{2c}.
$$

Since we have 8 parameters, we need to perturb the energy by $\pm c\vec{e}_i$ and we need to measure in 4 different bases, this means there are $8 \times 2 \times 4 = 64$ experiments we need to run for each iteration. 

One extra consideration is the number of shots. Typically it is easy to run the same circuit for many shots. We will use 8192, which means that the expected error in our expectation values due to this finite sampling is $1/\sqrt{8192} \approx 1\%$, which is more than accurate enough for our purposes. If we require greater accuracy, we may need to increase the number of shots. The results from the simulator and using the IBM quantum computers is shown in {numref}`fig:VQE`.

```{figure} ../images/VQE.pdf
---
name: fig:VQE
width: 65%
align: center
---

Results of the VQE optimisation using the simulator and on a real quantum device.
```



````{admonition} Exercises 4.1

**1.** Given the probabilities in the $Z$-basis, $p(|00\rangle), p(|01\rangle), p(|10\rangle), p(|11\rangle)$, write expressions for the expectation values $\langle 1 \otimes Z \rangle$, $\langle Z \otimes 1 \rangle$, and $\langle Z \otimes Z \rangle$.

**2.** Show that
```{figure} ../images/CZGate.png
---
width: 40%
align: center
---
```

**3.** (Code) Modify the VQE example code [VQE.ipynb](../downloads/VQE-simulator.ipynb) to use the following ansatz circuit

```{figure} ../images/VQE_alternative.png
---
width: 95%
align: center
---
```
How does this change affect the learning and the final accuracy?

````

````{admonition} Code downloads

[VQE.ipynb](../downloads/VQE.ipynb) - Jupyter notebook for running VQE on the simulator.

[HamiltonianParameters.txt](../downloads/HamiltonianParameters.txt) - Text file with the Hamiltonian parameters for the H-He$^+$ molecule. This is needed to run the VQE notebook.


````




---

## References
```{bibliography}
:filter: docname in docnames
```