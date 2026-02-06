(sec-qubit)=
# Quantum mechanics of a qubit

In this section we will set up the basics of quantum mechanics necessary for understanding how to use a quantum computer. This will hopefully be a recap of concepts you have already learnt, but may use unfamiliar notation. The best reference for this course is the book by Nielson and Chaung {cite}`nielsen_chuang_2010`. This is the standard text book in quantum computing and includes introductory chapters on linear algebra and quantum mechanics. It goes far beyond what we will cover and chapter 2 is the most relevant for us.

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

where $\alpha, \beta \in \mathbb{C}$ and $|\alpha|^2 + |\beta|^2 = 1$.

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


## The Bloch sphere


```{figure} ../images/BlochSphere.pdf
---
name: fig:bloch_sphere
width: 60%
align: center
---

Representation of the state of a single qubit on the Bloch sphere. The state $|\psi\rangle$ is parametrised by two angles $\theta$ and $\phi$. The computational basis state $|0\rangle$ and $|1\rangle$ are at the north and south pole. The other basis states we introduced lie along the x and y axes.
``` 


Given that physical states are normalised, a general state of a single qubit can be parametrised as 

$$
|\psi\rangle = e^{i\lambda} \left[ \cos \frac{\theta}{2} |0 \rangle + e^{i \phi} \sin \frac{\theta}{2} |1\rangle \right].
$$

However, the overall phase $e^{i\lambda}$ is not measurable and so can also be fixed to 1. Note, that when we introduce more qubits the *relative* phase will be measurable. Therefore, the single qubit state has the following parametrisation

$$
|\psi\rangle = \cos \frac{\theta}{2} |0 \rangle + e^{i \phi} \sin \frac{\theta}{2} |1\rangle,
$$

where $\theta \in [0, \pi)$ and $\phi \in [0,2\pi)$.

The angles $\theta$ and $\phi$ can be re-interpreted as parametrising the surface of a unit sphere, known as the Bloch sphere, as shown in {numref}`fig:bloch_sphere`. The $|0\rangle$ basis state is at the north pole of this Bloch sphere and the $|1\rangle$ is at the south pole. This picture will be useful for interpreting quantum states and the action of quantum gates.


````{admonition} Exercises 1.1.

**1.** Show that the X-basis, $\{|+\rangle, |-\rangle \}$, and the Y-basis, $\{|R\rangle, |L\rangle \}$, are orthonormal bases using both the column vector notation and Dirac bra-ket notation.

**2.** Write the state $|\phi\rangle = \gamma |R\rangle + \delta |L\rangle$ in the $Z$-basis $\{|0\rangle, |1\rangle \}$.

**3.** Find the Bloch angles $\theta, \phi$ for the state 

$$
|L\rangle = \frac{1}{\sqrt{2}}|0\rangle - i \frac{1}{\sqrt{2}} |1\rangle.
$$

````

## Quantum Gates

```{figure} ../images/single_qubit_gates.png
---
name: fig:single_qubit_gates
width: 100%
align: center
---

Action of single qubit gates on the Bloch sphere. The Hadmard gate allows us two go between the $|0\rangle$ and $|+\rangle$ states. The rotation gates correspond to rotations by $\theta$ around the x, y and z axes.
``` 

Now that we can represent the different states of a qubit, to do something useful we need to be able to change and manipulate these states. This is done using quantum gates. Single qubit gates are unitary operators acting on $\mathbb{C}^2$, the vector space for our states. A unitary operator is one whose inverse is its Hermitian conjugate, i.e., $\hat{U}\hat{U}^\dagger = \hat{U}^\dagger \hat{U} = 1$. These are usually easiest to represent as $2\times 2$ unitary matrices, where $U^\dagger$ is the conjugate transpose of the matrix $U$.

One very useful set of single qubit gates are the Pauli matrices $\{ 1, X, Y, Z \}$, defined by

$$
\begin{aligned}
    1 &\equiv \left(\begin{matrix} 1 & 0\\ 0 & 1 \end{matrix} \right), \qquad 1|0\rangle = |0\rangle, \;\; 1|1\rangle = |1\rangle\\
    \sigma^x = X &\equiv \left(\begin{matrix} 0 & 1\\ 1 & 0 \end{matrix} \right), \qquad X|0\rangle = |1\rangle, \;\; X|1\rangle = |0\rangle \\
    \sigma^y = Y &\equiv \left(\begin{matrix} 0 & -i\\ i & 0 \end{matrix} \right), \qquad Y|0\rangle = i|1\rangle, \;\; Y|1\rangle = -i|0\rangle \\
    \sigma^z = Z &\equiv \left(\begin{matrix} 1 & 0\\ 0 & -1 \end{matrix} \right), \qquad Z|0\rangle = |0\rangle, \;\; Z|1\rangle = -|1\rangle
\end{aligned}
$$

Depending on the situation it might be easier to use operator/Dirac notation or matrix/vector notation. In any case, you should be comfortable with switching between the two. As an example

$$
    X|-\rangle = - |-\rangle, \quad \text{equivalent to} \quad \left( \begin{matrix} 0& 1\\ 1& 0 \end{matrix}\right) \frac{1}{\sqrt{2}} \left(\begin{matrix} 1\\-1 \end{matrix} \right) = - \frac{1}{\sqrt{2}} \left(\begin{matrix} 1\\-1 \end{matrix} \right).
$$

Other important single qubit gates are the following,

$$
\begin{aligned}
\text{Hadamard} \qquad H & \equiv \frac{1}{\sqrt{2}} \left(\begin{matrix}1 & 1\\ 1 & -1 \end{matrix} \right),\\
\text{Phase} \qquad S & \equiv \left(\begin{matrix}1 & 0\\ 0 & i \end{matrix} \right),\\
\pi/8 \qquad T & \equiv \left(\begin{matrix}1 & 0\\ 0 & e^{i\pi/4} \end{matrix} \right).
\end{aligned}
$$

In particular, we will use the Hadamard and phase gates to change basis, as demonstrated in Fig.~\ref{fig: single qubit gates}. The Hadamard is also useful for creating superposition states. We will aslo use the following rotation gates

$$
\begin{aligned}
R_X(\theta) = \exp\left\{-i\frac{\theta}{2} X\right\} = \cos \frac{\theta}{2} 1 - i \sin \frac{\theta}{2} X, \\
R_Y(\theta) = \exp\left\{-i\frac{\theta}{2} Y\right\} = \cos \frac{\theta}{2} 1 - i \sin \frac{\theta}{2} Y, \\
R_Z(\theta) = \exp\left\{-i\frac{\theta}{2} Z\right\} = \cos \frac{\theta}{2} 1 - i \sin \frac{\theta}{2} Z.
\end{aligned}
$$

On the Bloch sphere, these correspond to rotations around a given axis. For instance $R_X(\theta)$ rotates the state around the x-axis by angle $\theta$, as shown in Fig.~\ref{fig: single qubit gates}. It turns out that we can write an arbitrary single qubit gate using three rotations plus an overall phase, i.e.

$$
U = e^{i\alpha} R_Z(\beta) R_Y(\gamma) R_Z(\delta).
$$ (eq:general_rotation)

Here we chose $Z$ and $Y$ rotation but we could have equivalently chosen any pair of $\{X, Y, Z\}$, see {cite}`nielsen_chuang_2010` for a proof. This combination of rotations generates an arbitrary unitary matrix in $SU(2)$, and the added phase promotes this to $U(2)$. When dealing with a single qubit, this phase can again be ignored since it is not measurable.

````{admonition} Exercises 1.2

**1.** Show that $HZH = X$ and $SHZHS^\dagger = Y$.

**2.** Show that $H$ and $S$ are unitary.

**3.** Given that 

$$
    R_Z(\theta) = \exp\left\{-i\frac{\theta}{2} Z\right\} = \cos \frac{\theta}{2} 1 - i \sin \frac{\theta}{2} Z.
$$

and that for a unitary matrix $U$, the matrix exponential has the following property

$$
    \exp (U^\dagger M U) = U^\dagger \exp (M) U
$$

Use this and the previous parts to show that 

$$
\begin{aligned}
R_X(\theta) = \exp\left\{-i\frac{\theta}{2} X\right\} = \cos \frac{\theta}{2} 1 - i \sin \frac{\theta}{2} X, \\
R_Y(\theta) = \exp\left\{-i\frac{\theta}{2} Y\right\} = \cos \frac{\theta}{2} 1 - i \sin \frac{\theta}{2} Y.
\end{aligned}
$$

````

(sec:measurement)=
## Measuring quantum states

Now we know how to represent the state of qubit and how to manipulate it using quantum gates, all that remains is to measure the qubit. Measurement is perhaps the most unsettling aspect of quantum mechanics. Despite the deterministic nature of complex vectors and unitary matrix multiplication, our observation of a quantum system is necessarily probabilistic. 

Let us consider a single qubit in the state

$$
    |\psi\rangle = \alpha |0 \rangle + \beta |1 \rangle,
$$

which is properly normalised such that $|\alpha|^2 + |\beta|^2 = 1$. If we now ``look at" the qubit, we will find that it is in the state $|0\rangle$ or $|1\rangle$ with probabilities $|\alpha|^2$ and $|\beta|^2$, respectively, as illustrated in {numref}`fig:measurement`. That is, while the state is in a superposition, when we measure it has to be in either $|0\rangle$ or $|1\rangle$, and this becomes the new state of the system. This is sometimes referred to as the wavefunction collapsing. Which of the two possibilities we actually measure and which way the state collapses is probabilistic. To paraphrase: when we measure a qubit we get a classical bit, whose value is probabilistic.


```{figure} ../images/MeasurementSchematic.pdf
---
name: fig:measurement
width: 75%
align: center
---

Schematic of measurement of a qubit in quantum mechanics. When we measure the qubit the state collapses into one of the two basis states. The probability of each possibility is given by the absolute square of the coefficients in the original state. 
``` 


Let us be a bit more formal. Above, there was something special about the states $|0\rangle$ and $|1\rangle$, which is due to a choice in how we measured. We say we "measured in the computational basis". More generally, measurements are associated with operators $\{M_n\}$, such that $\sum_n M_n^\dagger M_n = 1$. The specific operators $M_n$ are specified by our measurement apparatus.

When we measure, the state of the system becomes,

$$
    |\psi'\rangle = \frac{M_n |\psi\rangle}{\sqrt{\langle\psi|M^\dagger_n M_n |\psi\rangle}}, \quad \text{with probability} \quad p(n) = \langle \psi | M_n^\dagger M_n |\psi \rangle.
$$

In the computational basis, this amounts to the choice $M_0 = |0\rangle\langle 0|$, $M_1 = |1\rangle \langle 1 |$, which project onto the two computational basis states. 

In a quantum computer the measurement operators are fixed and we always measure in the computational basis. However, we can measure in different bases, say the X-basis $\{|+\rangle, |-\rangle \}$ by first applying a unitary Hadamard gate. That is,
measuring 

$$
|\psi\rangle = \alpha |+\rangle + \beta |-\rangle, \quad \text{with} \quad M_+ = |+\rangle\langle +|, M_- = |-\rangle\langle -|,
$$

is equivalent to measuring,

$$
    |\psi'\rangle = H |\psi\rangle = \alpha |0\rangle + \beta |1\rangle, \quad \text{with} \quad M_0 = |0\rangle\langle 0|, M_1 = |1\rangle \langle 1|.
$$

And so we can always measure in a different basis by first rotating our state.

Often when we measure a qubit, our goal will be to compute an expectation value of some Hermitian (observable) operator. As the simplest case consider the expectation value $\langle Z \rangle = \langle \psi | Z | \psi \rangle$. Here we use the shorthand for expectation values with respect to a given state, i.e., $\langle \cdot \rangle = \langle \psi | \cdot |\psi \rangle$. The Z Pauli matrix is diagonal in the computational basis (meaning the basis states are eigenvectors) and we have $Z |0\rangle = |0\rangle$ and $Z|1\rangle = -|1\rangle$. This makes the expectation value particular easy to compute in terms of the probabilities of measuring each basis state, that is

$$
\langle Z \rangle = p(|0\rangle) - p(|1\rangle).
$$

For the other Pauli operators we can make a basis change to make them diagonal in the computational basis. That is $\langle X \rangle = (\langle \psi| H) Z (H |\psi\rangle)$, and $\langle Y \rangle = (\langle \psi|SH) Z (HS^\dagger |\psi\rangle)$. In the general case, let $H$ be the Hermitian operator with eigenstates $|\psi_i\rangle$ and eigenvalues $E_i$, then the expectation value is given by

$$
    \langle H \rangle = \sum_i E_i p(|\psi_i\rangle).
$$


## Quantum circuit notation

As we go further, and particularly when we add more qubits, working with vectors and matrices can become a bit cumbersome and hard to read. We will therefore use a graphical *quantum circuit* notation.

Let us look at a couple of examples for a single qubit. The first example is

```{figure} ../images/hadamard_diagram.png
---
width: 45%
align: center
---
```

Here we start with the state $|0\rangle$ and then apply the hadamard gate to get the state $|\psi\rangle$. In the quantum circuit notation "time" goes from left to right. The qubit is symbolised by a black "wire" and the hadamard gate by a box with the letter $H$. As a second example, consider

```{figure} ../images/two_gates_diagram.png
---
width: 55%
align: center
---
```

Here we apply the X gate first and then the hadamard gate. Is is important to notice that our direction of "time" is opposite to matrix multiplication.

Finally, we can also introduce measurements into our quantum circuit diagrams. for example

```{figure} ../images/measurement_diagram.png
---
width: 25%
align: center
---
```

where the final box symbolises a "meter" which performs the measurment. In contrast to the previous examples, this circuit diagram does not represent a quantum state. Instead this is a complete quantum program including measurement. The outcome of this program with either be 0 or 1 (corresponding to measuring $|0\rangle$ or $|1\rangle$), with probabilities set by the state immediately before measuring.

````{admonition} Exercises 1.3

**1.** We previously showed that $HZH = Z$ and $SHZHS^\dagger = Y$. Hence show that 

$$
    \langle \psi | X | \psi \rangle = \langle \psi_x | Z |\psi_x\rangle, \qquad \langle \psi | Y | \psi \rangle = \langle \psi_y | Z |\psi_y\rangle,
$$

where $|\psi_x\rangle = H |\psi\rangle$ and $|\psi_y\rangle = HS^\dagger |\psi\rangle$.

**2.** Given the state $|\psi\rangle = \alpha |0\rangle + \beta |1\rangle$, show that 

$$
\langle \psi | Z | \psi \rangle = |\alpha|^2 - |\beta|^2 = p(|0\rangle) - p(|1\rangle).
$$

**3.** A general single-qubit rotation can be written as $U = R_Z(\beta) R_Y(\gamma) R_Z(\delta)$, up to a global phase. Write this decomposition of a rotation gate applied to the state $|0\rangle$ in quantum circuit notation.

````


---

## References
```{bibliography}
:filter: docname in docnames
```