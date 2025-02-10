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
\begin{equation}
E = \min_{|\psi\rangle} \frac{\langle \psi | \hat{H} |\psi\rangle}{\langle \psi | \psi \rangle} = \min_{|\psi\rangle}\langle \psi | \hat{H} |\psi\rangle,
\end{equation}
where in the second equality we used the fact that the state $|\psi\rangle$ is normalised. On the quantum computer we always start with the zero state $|000\cdots \rangle$, and so we rephrase the problem as finding the quantum circuit $V$ such that $|\psi\rangle = V |000\cdots\rangle$, i.e., 
\begin{equation}\label{eq: VQE general circuit}
    \pbox{\textwidth}{
    \begin{quantikz}[row sep={0.75cm,between origins}]
    \lstick{\ket{0}} & \gate[4,nwires=4]{\quad V \quad} & \qw \\
    \lstick{\ket{0}} & \qw & \qw \\
    \lstick{\ket{0}} & \qw & \qw \\
    \hspace{-25pt}\vdots & & \vdots \\
    \end{quantikz},
    }
\end{equation}
minimises the energy.

The quantum circuit in Eq~\eqref{eq: VQE general circuit} contains a large multiple qubit gate. As we mentioned in a previous section, we are able to approximate a general multi-qubit gate using only the single qubit rotations and the CNOT gate. The art of VQE is to come up with the best quantum circuit to approximate $V$.

The basic setup of VQE consists of the following three steps:
\begin{enumerate}
    \item Create an ansatz quantum circuit with variable parameters that can be optimised.
    \item Measure the quantum circuit to extract the value of the energy.
    \item Use a classical optimisation algorithm to propose updated parameters for the quantum circuit ansatz, then repeat from 1.
\end{enumerate}
We will discuss these three steps in greater detail in the following three sections.







---

## References
```{bibliography}
:filter: docname in docnames
```