---
numbering:
  title:
    enabled: true
    offset: 0
  headings: true
  figure:
    enumerator: '2.%s'
  equation:
    enumerator: '2.%s'
---
(sec-multi-qubit)=
# Multiple qubits, entanglement, and quantum hardware

So far we have learnt the quantum mechanics necessary to describe, manipulate and measure a single qubit. However, a single qubit is not so much use. While it is possible to do interesting things with this single qubit, in the end this boils down to two dimensional vector and matrix multiplication, which our classical computers are incredibly efficient at doing. The real advantage of quantum computers comes when we have multiple qubits and generate quantum entanglement between them. In this section we will generalise our vector space to accommodate multiple qubits, we will introduce multi-qubit gates and measurements, and we will program our first quantum circuit with entanglement, which can be run on real quantum computers.


## Quantum states of multiple qubits

When we introduce multiple qubits, the quantum states live in a space that is the tensor product of the individual qubit spaces. 

For example, for *two qubits* the relevant vector space is $\mathbb{C}^4 = \mathbb{C}^2 \otimes \mathbb{C}^2$, and the computational basis is

$$
    \{|00\rangle, |01\rangle, |10\rangle, |11\rangle \} = \{|0\rangle\otimes |0\rangle, |0\rangle\otimes |1\rangle, |1\rangle\otimes |0\rangle, |1\rangle\otimes |1\rangle \}.
$$

A general state can be written as 

$$
    |\psi\rangle = \psi_{00} |00\rangle + \psi_{01} |01 \rangle + \psi_{10} |10\rangle + \psi_{11} |11\rangle,
$$

where $|\psi_{00}|^2 + |\psi_{01}|^2 + |\psi_{10}|^2 + |\psi_{11}|^2 = 1$, due to normalisation.

For *three qubits* we have $\mathbb{C}^8 = \mathbb{C}^2 \otimes \mathbb{C}^2 \otimes \mathbb{C}^2$, the computational basis is

$$
    \{|000\rangle, |001\rangle, |010\rangle, |011\rangle, |100\rangle, |101\rangle, |110\rangle, |111\rangle\},
$$

and 

$$
    |\psi\rangle = \sum_{i,j,k=0}^1 \psi_{ijk} |ijk\rangle,
$$

where $\sum_{ijk} |\psi_{ijk}|^2 = 1$. And similarly for more qubits. Importantly, the size of the vector space grows exponentially with the number of qubits, i.e., for $N$ qubits the vector space is $\mathbb{C}^{2^N}$. The exponential growth of the vector space is where the advantage of quantum computers lies. Put another way, as we increase the number of qubits, the size of matrices and vectors will at some point become too large to store in memory on a classical computer. Importantly, there are ways that we can manipulate these qubits that cannot be simulated efficiently on a classical computer.

## Kronecker product operators

Once we have multiple qubits, the simplest thing we could do is to manipulate them separately while still describing them as a combined system of multiple qubits. These operators correspond to Kronecker products of single qubit operators. For example,

```{figure} ../images/Kronecker2Qubit.png
---
width: 75%
align: center
---
```

Here we have used a quantum circuit diagram with multiple qubits, represented by the black "wires" running from left to right. The $H$ gate acting on only the first qubit is equivalent to acting on both qubits with the operator $H\otimes 1$. In the end, the state is still in tensor product form, that is, the multi-qubit state is the tensor product of two single qubit states.

Let us consider another example, this time using three qubits and multiple single qubit gates:

```{figure} ../images/Kronecker3Qubit.png
---
width: 100%
align: center
---
```

Again we end up with a tensor product state. 

Often we will use a short hand for single qubit gates, where we write the operator with an index, e.g., $H_2$ which is a Hadamard gate acting on the second qubit. If we had three qubits, this would be shorthand for $H_2 = 1 \otimes H \otimes 1$. We often don't need to think about this tensor product with identities, and can simply think of the gate acting on a single qubit. However, mathematically our state lives in a larger vector space, e.g. $\mathbb{C}^8$, and so it doesn't make sense to apply a $2\times 2$ matrix to an 8-dimensional vector.

The Kronecker product of two single qubit gates in matrix form is given by

$$
    \left(\begin{matrix} 
    a_{11} & a_{12} \\
    a_{21} & a_{22}
    \end{matrix}\right) \otimes
    \left(\begin{matrix} 
    b_{11} & b_{12} \\
    b_{21} & b_{22}
    \end{matrix}\right)
    =
    \left(\begin{matrix} 
    a_{11}b_{11} & a_{11} b_{12} & a_{12}b_{11} & a_{12}b_{12} \\
    a_{11}b_{21} & a_{11} b_{22} & a_{12}b_{21} & a_{12}b_{22} \\
    a_{21}b_{11} & a_{21} b_{12} & a_{22}b_{11} & a_{22}b_{12} \\
    a_{21}b_{21} & a_{21} b_{22} & a_{22}b_{21} & a_{22}b_{22}
    \end{matrix}\right).
$$

In general this can be nicely summarised in block form:

$$
    A \otimes B = \left(\begin{matrix} 
    a_{11}B & \cdots & a_{1n}B \\
    \vdots & \ddots & \vdots \\
    a_{m1}B & \cdots & a_{mn}B
    \end{matrix}\right),
$$

where $A$ is a $m\times n$ matrix, and $B$ is a $r \times s$ matrix.


## Measuring multiple qubits

Measuring multiple qubits is similar to the single qubit case: when we measure the system of many qubits we will find it in one of the computational basis states. The probability of being in each of the basis states is given by the probability amplitude squared, e.g., $p(|010\rangle) = |\psi_{010}|^2$. 

```{figure} ../images/Measurement2qubitSchematic.svg
---
name: fig:measurement_2_qubit
width: 85%
align: center
---

Schematic of measurement of two qubits in quantum mechanics. As with a single qubit, measuring the system causes it to collapse into one of the basis states with probabilities determined by the absolute square of the coefficients.
``` 

There are cases when we might want to use multiple qubits to do a computation but not measure all of them. In this case, the probability is simply the sum of all the probabilities that have that outcome for the measured qubits. For example, consider three qubits, but we only measure the first qubit. The probabilities of measuring the two states of that qubit are

$$
\begin{aligned}
    p(|0\rangle_1) = p(|000\rangle) + p(|001\rangle) + p(|010\rangle) + p(|011\rangle),\\
    p(|1\rangle_1) = p(|100\rangle) + p(|101\rangle) + p(|110\rangle) + p(|111\rangle).
\end{aligned}
$$ (eq:reduced_measurement)


````{admonition} Exercises 2.1

**1.** Compute the following Kronecker products: $X\otimes X$, $Z \otimes Z$, $1 \otimes X$, $Z \otimes X \otimes Y$.

**2.** Remembering that for a Hermitian operator $\hat{O}$ with eigenvalues $E_i$ and corresponding eigenvectors $|\psi_i\rangle$,

$$
    \langle \hat{O} \rangle \equiv \langle \psi | \hat{O} | \psi \rangle = \sum_i E_i p(|\psi_i\rangle)
$$

and given the probabilities in the $Z$-basis, $p(|00\rangle), p(|01\rangle), p(|10\rangle), p(|11\rangle)$, write expressions for the expectation values $\langle 1 \otimes Z \rangle$, $\langle Z \otimes 1 \rangle$, and $\langle Z \otimes Z \rangle$.

````


## Entangling gates (CNOT)

So far we have only acted separately on multiple qubits. While describing these operations may seem to require matrices that are exponentially large in the number of qubits, we must remember that we could always treat each qubit separately. Therefore, simulating $N$ qubits on a classical computer is only $N$ times harder than a single qubit, not exponentially. To do something genuinely useful on a quantum computer we need to introduce operators that act on multiple qubits and that aren't Kronecker product operators. This type of operator can generate quantum entanglement and stops us from being able to classically simulate the problem.

There are several important multi-qubit gates, but we will only need one. This is the CNOT or controlled-NOT (also called CX or controlled-X) gate. This gate is represented as

```{figure} ../images/CNOTGate.png
---
width: 45%
align: center
---
```

Importantly, this gate is not a Kronecker product operator, that is, $\text{CNOT} \neq A \otimes B$ for any single qubit gates $A$ and $B$.

The CNOT gate "flips" (applies X) to the second qubit if the first is in the $|1\rangle$ state or does nothing if the first qubit is in the $|0\rangle$ state. The first qubit is referred to as the *control qubit*, and the second as the *target qubit*. Explicitly, we have

$$
\begin{aligned}
\text{CNOT} |00\rangle &= |00 \rangle, \\
\text{CNOT} |01\rangle &= |01 \rangle, \\
\text{CNOT} |10\rangle &= |11 \rangle, \\
\text{CNOT} |11\rangle &= |10 \rangle.
\end{aligned}
$$

The way the CNOT gate acts on the target qubit is different depending on the state of the control qubit. It is for this reason that the gate cannot be split into single qubit gates. The state of the qubits can also become entangled after applying the CNOT gate. We will see in the next section what it means to be entangled.

For single qubit gates we introduced an array of different single qubit gates, and even found that a general single qubit gate can be continuously parametrised by three angles. However, here we only introduce a single multi-qubit gate. It turns out that the CNOT gate plus single qubit rotations is a *universal gate set*. This means that any quantum gate (unitary matrix) can be written exactly as a sequence of these gates. (If we restrict ourselves to a *finite* set of single-qubit gates, such as $H$ and $T$, any gate can still be approximated to arbitrary accuracy.) Therefore any other two-qubit gates, or gates involving more than two qubits can be written in terms of CNOT and single qubit rotations. This is an extremely powerful theorem in quantum computing that we will not show here. The proof is provided in Ref.{cite}`nielsen_chuang_2010`. 

The CNOT gate is not unique in this respect: there are other choices we could have made for our entangling gate. We chose the CNOT because it is the most common choice in textbooks and makes circuits easy to read. Real devices often implement a different entangling gate natively. For example, current IBM and Quantum Inspire devices use the controlled-Z (CZ) gate, which we will meet in {numref}`sec:explicit_example`. When we run on hardware, Qiskit automatically rewrites our CNOTs in terms of the device's native gates.


## My first circuit (Bell states)

With all of this setup, let us now turn to our first proper multi-qubit quantum circuit. This will showcase what the CNOT gate is doing and what we mean by quantum entanglement. Let us consider the following circuit

```{figure} ../images/BellCircuit.png
---
name: eq:Bell_state_circuit
width: 30%
align: center
---
```

At the start of the circuit we are in the state

$$
    |\psi_0\rangle = |00\rangle = |0\rangle \otimes |0\rangle.
$$

This is the starting state for the quantum computer. Any quantum circuit we run will always start with all qubits in the zero state. Next we apply a Hadamard gate to the first qubit. This is a Kronecker product operator and results in the state

$$
\begin{aligned}
    |\psi_1\rangle = H\otimes 1 |00\rangle &= H|0\rangle \otimes |0\rangle\\
    & = \frac{1}{\sqrt{2}}(|0\rangle + |1\rangle) \otimes |0\rangle \\
    & = \frac{1}{\sqrt{2}}\left( |00\rangle + |10\rangle \right).
\end{aligned}
$$

Next we apply the CNOT gate to get the final state before measuring

$$
\begin{aligned}
|\psi\rangle &= \text{CNOT} \cdot \frac{1}{\sqrt{2}} \left( |00\rangle + |10\rangle \right) \\
& = \frac{1}{\sqrt{2}} \left( |00\rangle + |11\rangle \right).
\end{aligned}
$$

This state is no longer a tensor product of single qubit states, that is, $|\psi\rangle \neq |\phi_1\rangle \otimes |\phi_2\rangle$, for any single qubit state $|\phi_1\rangle$, and $|\phi_2\rangle$.

Finally, we measure both qubits. The probabilities of measuring each of the states are

$$
    p(|00\rangle) = \frac{1}{2}, \quad p(|01\rangle) = 0, \quad  p(|10\rangle) = 0, \quad  p(|11\rangle) = \frac{1}{2}.
$$

To understand how strange this is, consider measuring the two qubits separately. We find that $p(|0\rangle_1) = p(|0\rangle_2) = 1/2$ and $p(|1\rangle_1) = p(|1\rangle_2) = 1/2$. That is, both qubits have 50\% chance of being in either state, just like a fair coin flip. However, when we measure them together we find that the two qubits will always agree. There is zero probability of being in the $|01\rangle$ state or the $|10\rangle$ state. If we measure one of the qubits first, then the outcome of measuring the other qubit will be fixed. The two qubits are said to be entangled.

The state we have constructed is one of the four Bell states

$$
\begin{aligned}
    |\Phi^+ \rangle &= \frac{1}{\sqrt{2}} \left( |00\rangle + |11\rangle \right), \\
    |\Phi^- \rangle &= \frac{1}{\sqrt{2}} \left( |00\rangle - |11\rangle \right), \\
    |\Psi^+ \rangle &= \frac{1}{\sqrt{2}} \left( |01\rangle + |10\rangle \right), \\
    |\Psi^- \rangle &= \frac{1}{\sqrt{2}} \left( |01\rangle - |10\rangle \right).
\end{aligned}
$$

These states form an alternative orthonormal basis for the vector space of two qubits. These states are all "maximally entangled". There is no basis in which we can measure a single qubit and get a certain answer. In fact, measuring in any basis we find that we have 50\% probability of measuring one of the qubits in either of its two states. However, once we measure one of the qubits, the state of the other qubit is certain.



````{admonition} Exercises 2.2

**1.** Modify the Bell State circuit in the following way:

```{figure} ../images/GHZCircuit.png
---
width: 30%
align: center
---
```

Write down the three-qubit state this quantum circuit diagram represents. This state is known as the Greenberger–Horne–Zeilinger (GHZ) state.
````

## Programming with Qiskit

We are now in a position to simulate our first quantum circuit, and even to run it on a real quantum computer. To do so we use the Python package [Qiskit](https://www.ibm.com/quantum/qiskit) {cite}`Qiskit`, the most widely used framework for programming quantum computers. See [Setting up Python and Qiskit](../outline/pythonEnvironment.md) to install it. All the code in this section is in the notebook [Code 1: Bell state](../../notebooks/01-bell-state.ipynb).

First we build the circuit. A `QuantumCircuit(2)` has two qubits, both starting in $|0\rangle$, and we add gates one at a time:

```{code} python
from qiskit import QuantumCircuit

qc = QuantumCircuit(2)
qc.h(0)          # Hadamard on qubit 0
qc.cx(0, 1)      # CNOT with control qubit 0 and target qubit 1
qc.measure_all() # measure every qubit
qc.draw("mpl")   # draw the circuit diagram
```

This draws the diagram in {numref}`fig:bell_state_qiskit_circuit`(a), which is indeed the circuit we want. The circuit is run by a *primitive*. The **Sampler** runs the circuit many times and returns the counts of each measured bit string:

```{code} python
from qiskit.primitives import StatevectorSampler

shots = 8192
sampler = StatevectorSampler()
result = sampler.run([qc], shots=shots).result()
counts = result[0].data.meas.get_counts()
print(counts)    # e.g. {'00': 4156, '11': 4036}
```

A *shot* is a single run of the circuit, the outcome of which is a single basis state: 00, 01, 10 or 11. By running many shots we estimate the probability of each outcome, just like flipping a coin many times to estimate the probability of heads. Dividing the counts by the number of shots and plotting them gives {numref}`fig:bell_state_qiskit_circuit`(b).

```{figure} ../images/qiskit_BellCircuit.png
---
name: fig:bell_state_qiskit_circuit
width: 95%
align: center
---
(a) The Bell state circuit drawn by Qiskit. The `meas` line holds the classical measurement results. (b) Histogram of the measurement outcomes from 8192 shots on the simulator.
```

We don't get exactly 50% because we only measured 8192 times. The statistical error on an estimated probability $p$ from $N$ shots is $\sqrt{p(1-p)/N}\approx 0.006$. As we increase the number of shots, the probabilities approach 50%.

```{important}
Qiskit writes bit strings **right to left**: in `"01"` the rightmost bit is qubit 0. In these notes we write states left to right, $|q_0 q_1\rangle$. So the state we would write as $|10\rangle$ (qubit 0 in $|1\rangle$) appears in Qiskit as `"01"`. For the Bell state this makes no difference, but it matters for less symmetric circuits.
```

### Running on a real quantum computer

The same circuit can be run on a real quantum computer. IBM and Quantum Inspire both offer free access. See [Running on real quantum computers](../outline/hardwareAccess.md) for how to set up an account. Real devices only implement a small set of *native* gates, between certain pairs of physical qubits. So our circuit must first be **transpiled**, i.e. rewritten for the device, before running it:

```{code} python
from qiskit_ibm_runtime import QiskitRuntimeService, SamplerV2 as Sampler
from qiskit.transpiler import generate_preset_pass_manager

service = QiskitRuntimeService()          # loads your saved IBM account
backend = service.least_busy(operational=True, simulator=False)

pm = generate_preset_pass_manager(backend=backend, optimization_level=3)
qc_device = pm.run(qc)                    # rewrite for the device's native gates

result = Sampler(mode=backend).run([qc_device], shots=shots).result()
counts = result[0].data.meas.get_counts()
```

Before using precious QPU time, you can test this code on a *fake backend*: a noisy simulation of a real device, e.g. `backend = FakeFez()` from `qiskit_ibm_runtime.fake_provider`. The notebook does exactly this.

Running on a real device takes much longer than the simulator. The job has to be sent to the provider's servers, wait in a queue with everyone else's jobs, and then run. When plotting the histogram of the measurements you find something like {numref}`fig:bell_state_real`. Not only are the values further from 50%, but there are also non-zero probabilities for the states 01 and 10. Current devices are not perfect, and suffer from noise in the gates and the measurements. Regardless, we have run our first circuit on a quantum computer and created an entangled quantum state!

```{figure} ../images/BellState_IBM.svg
---
name: fig:bell_state_real
width: 60%
align: center
---

Histogram of the measurement outcomes for the Bell state circuit run on an IBM quantum computer. The non-zero probabilities of 01 and 10 come from noise in the device.
```

````{admonition} Exercises 2.3

**1.** Modify the Bell state example code in [Code 1](../../notebooks/01-bell-state.ipynb) to implement the following circuit

```{figure} ../images/GHZCodeExercise.png
---
width: 40%
align: center
---
```

This is the GHZ circuit from Exercises 2.2 but including measurements. Check that the probabilities obtained in the simulator (and on a real device) match the expected values for this state. 
````



````{admonition} Code Download

Here are links to download Jupyter notebooks for the code from this lecture:

* [Code 1: Bell state](../../notebooks/01-bell-state.ipynb) - running the Bell state circuit on a simulator.

See [Setting up Python and Qiskit](../outline/pythonEnvironment.md) for how to set up the environment to run these notebooks, and all the code in this course.

````
