(sec-qubit)=
# Quantum mechanics of a qubit

In this section we will set up the basics of quantum mechanics necessary for understanding how to use a quantum computer. This will hopefully be a recap of concepts you have already learnt, but may use unfamiliar notation. The best reference for this course is the book by Nielson and Chaung [@nielsen_chuang_2010]. This is the standard text book in quantum computing and includes introductory chapters on linear algebra and quantum mechanics. It goes far beyond what we will cover and chapter 2 is the most relevant for us.

If you have already taken a course on quantum mechanics you may be familiar with the wave mechanics of a single particle, e.g., solving the Schrödinger equation for a particle in a box. This involves differentiable complex wave functions and the solution to a partial differential equation. In the course we will instead be interested in the quantum mechanics of qubits. This will be described by finite complex vectors that represent the quantum many-body state of the qubits and unitary matrices that represent the relevant operators.

## Quantum states of a qubit

The building blocks of quantum computers are called qubits, which are two-level quantum systems. That is, a qubit has two orthogonal states, commonly denoted

$$
    |0\rangle, \qquad \text{and} \qquad |1\rangle.
$$

These are the analogues of the classical states of a bit: 0 or 1. However, quantum mechanically, a qubit can exist in superpositions of these states. A general state of a qubit can be written as

$$
    |\psi\rangle = \alpha|0\rangle + \beta |1 \rangle,
$$

where $\alpha, \beta \in \mathbb{C}$ and $|\alpha|^2 = |\beta|^2 = 1$.

The state of the qubit lives in the vector space $\mathbb{C}^2$ which is equipped with the standard inner product (making it an inner product space, and actually a Hilbert space). The inner product can be defined by 

$$
    \langle \phi | \psi \rangle = \phi_0^* \psi_0 + \phi_1^* \psi_1,
$$

where $|\phi\rangle = \phi_0 |0\rangle + \phi_1|1\rangle$, and $|\psi\rangle = \psi_0|0\rangle + \psi_1|1\rangle$. This form for the inner product can be nicely summarised using the Dirac bra-ket notation, where $\langle \phi | = \phi_0^* \langle 0 | + \phi_1^* \langle 1|$, with $\langle 0 | 1 \rangle = \langle 1 | 0 \rangle = 0$, and $\langle 0 | 0 \rangle = \langle 1 | 1 \rangle = 1$. This inner product also defines a norm

$$
    | |\psi\rangle | = \sqrt{ \langle \psi | \psi \rangle }.
$$

The physical pure states are those with unit norm.

The two states $\{|0\rangle, |1\rangle\}$ form an orthonormal basis for the two-dimensional vector space $\mathbb{C}^2$, and so we could instead use the column vector notation

$$
    |0\rangle \equiv \left(\begin{matrix}1\\0\end{matrix}\right), \qquad |1 \rangle \equiv \left(\begin{matrix}0\\1\end{matrix}\right).
$$

This is usually referred to as the computational basis (other names include: logical basis, natural basis or Z-basis). Depending on the circumstance, it will be useful to switch between Dirac notation and column vector notation.

As with any vector space, the choice of basis is not unique. Two other useful choices of basis are the X-basis, $\{|+\rangle, |-\rangle \}$, given by

$$
    |+\rangle = \frac{1}{\sqrt{2}}\left( |0\rangle + |1\rangle \right) \equiv \frac{1}{\sqrt{2}} \left( \begin{matrix}1\\1 \end{matrix} \right), \qquad |-\rangle = \frac{1}{\sqrt{2}}\left( |0\rangle - |1\rangle \right) \equiv \frac{1}{\sqrt{2}} \left( \begin{matrix}1\\-1 \end{matrix} \right),
$$

and the Y-basis, $\{ |R\rangle, |L \rangle\}$,

$$
    |R\rangle = \frac{1}{\sqrt{2}}\left( |0\rangle + i|1\rangle \right) \equiv \frac{1}{\sqrt{2}} \left( \begin{matrix}1\\i \end{matrix} \right), \qquad |L\rangle = \frac{1}{\sqrt{2}}\left( |0\rangle -i |1\rangle \right) \equiv \frac{1}{\sqrt{2}} \left( \begin{matrix}1\\-i \end{matrix} \right).
$$

You can check using the above definition of the inner product that these are also orthonormal bases.
We can then write a given state in any basis depending on which is most convenient, e.g.,

$$
    |\psi \rangle = \alpha |0\rangle + \beta |1\rangle = \frac{\alpha + \beta}{\sqrt{2}} |+\rangle + \frac{\alpha - \beta}{\sqrt{2}}|-\rangle \equiv \left( \begin{matrix}\alpha\\ \beta \end{matrix} \right).
$$