# Multiple qubits, entanglement, and IBMQ

So far we have learnt the quantum mechanics necessary to describe, manipulate and measure a single qubit. However, a single qubit is not so much use. While it is possible to do interesting things with this single qubit, in the end this boils down to two dimensional vector and matrix multiplication, which our classical computers are incredibly efficient at doing. The real advantage of quantum computers comes when we have multiple qubits and generate quantum entanglement between them. In this section we will generalise our vector space to accommodate multiple qubits, we will introduce multi-qubit gates and measurements, and we will program our first quantum circuit with entanglement that can be run on IBM quantum computers.


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

For *three qubits* we have $\mathbb{C}^8 = \mathbb{C}^2 \otimes \mathbb{C}^2 \otimes \mathbb{C}^2$, the computation basis is

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
    a_{22}b_{21} & a_{22} b_{22} & a_{22}b_{21} & a_{22}b_{22}
    \end{matrix}\right).
$$

In general this can be nicely summarised in block form:

$$
    A \otimes B = \left(\begin{matrix} 
    a_{11}B & \cdots & a_{1n}B \\
    \vdots & \ddots & \vdots \\
    a_{m1}B & \cdots & a_{mm}B
    \end{matrix}\right),
$$

where $A$ is a $m\times n$ matrix, and $B$ is a $r \times s$ matrix.


## Measuring multiple qubits

Measuring multiple qubits is similar to the single qubit case: when we measure the system of many qubits we will find it in one of the computational basis states. The probability of being in each of the basis states is given by the probability amplitude squared, e.g., $p(|010\rangle) = |\psi_{010}|^2$. 

```{figure} ../images/Measurement2qubitSchematic.pdf
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

**2.** Remembering that for a Hermitian operator $H$ with eigenvalues $E_i$ and corresponding eigenvectors $|\psi_i\rangle$,

$$
    \langle H \rangle \equiv \langle \psi | H | \psi \rangle = \sum_i E_i p(|\psi_i\rangle)
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

For single qubit gates we introduced an array of different single qubit gates, and even found that a general single qubit gate can be continuously parametrised by three angles. However, here we only introduce a single multi-qubit gate. It turns out that the CNOT gate plus single qubit rotations is a *universal gate set*. This means that any quantum gate (unitary matrix) can be approximated to arbitrary accuracy by a sequence of these gates. Therefore any other two-qubit gates, or gates involving more than two qubits can be written in terms of CNOT and single qubit rotations. This is an extremely powerful theorem in quantum computing that we will not show here. The proof is provided in Ref.{cite}`nielsen_chuang_2010`. 

The CNOT gate is not unique in this respect: there are other choices we could have made for our entangling gate. We primarily chose the CNOT because this is the gate that IBM have implemented in there quantum computers. 
%(Add back when chapter added!)We will encounter another in section {numref}`sec:explicit_example`.


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

%```{code} python 
%:number-lines: 1
%
%from qiskit import QuantumCircuit, Aer, execute
%```


## Using pennylane and IBMQ

We are now in a position to simulate our first quantum circuit and to also run it on a real quantum computer. To do so we will be using a python package called [pennylane](https://pennylane.ai) {cite}`pennylane`. We will then run our code on one of IBM's quantum computers, for which there is free access through IBM's quantum experience and [qiskit](https://www.ibm.com/quantum/qiskit) {cite}`Qiskit`, which we can connect to pennylane. In this section we will go through how to use pennylane to program and run the Bell state circuit we discussed in the previous section.

To start, let us install pennylane using your favourite python installation or virtual environment. This is most easily done using pip:

```python
 >> pip install pennylane --upgrade
```

See pennylane.ai for more details, which also includes detailed documentation. I would recommend using a Jupyter notebook, but you can also use python scripts if preferred.

Our first code is included in the file named "BellState-simulator.ipynb", which is provided on the course moodle page. To start we need to import the relevant python packages using the following

```{code} python 

import pennylane as qml
```

This imports the pennylane package under the shorthand qml (quantum machine learning). This shorthand comes from the historical focus of pennylane as a quantum machine learning package with built in autodifferentiation, but it has become much more general than this. Next we have to set the device for running the quantum circuit

```{code} python 

dev = qml.device("default.qubit", wires=2)
```

To start with we are using a simulator that will run directly on our computer. We specify that we will only be using two qubits (called wires). To run our code on a real quantum computer, we will later change this line.

```{figure} ../images/pennylane_BellCircuit.png
---
name: fig:bell_state_qiskit_circuit
width: 90%
align: center
---

(a) Quantum circuit diagram drawn by pennylane. (b) Histogram of the measurement outcomes for the quantum circuit. On the x-axis are the different basis states that were measured. The y-axis shows the inferred probability of measuring each of the basis states.
``` 

After this setup we will next build the quantum circuit

```{code} python 

@qml.qnode(dev)
def circuit():
    qml.Hadamard(wires=0)  # apply a Hadamard gate on qubit 0
    qml.CNOT(wires=[0, 1])  # apply CNOT from qubit 0 to 1

    return qml.counts()  # measure all qubits and return the counts
```

The decorator in the first line lets pennylane know that the following function defines a quantum circuit and that it should be run on the device "dev". We then define the circuit by adding a Hadamrd gate to qubit 0 and a CNOT between qubit 0 and 1. Finally, the function returns the counts. When we call this function, the circuit will be run and the function returns the measured counts.

Now that we have defined the circuit, we can check that we have defined it correctly by plotting the corresponding circuit diagram

```{code} python
qml.drawer.use_style("black_white")
qml.draw_mpl(circuit)()  # plot the circuit diagram
```

This produces the image shown in {numref}`fig:bell_state_qiskit_circuit`(a), which is indeed the circuit we want to run.

We are now in the position to run the circuit, which is done with one line:

```{code} python
counts = circuit(shots=8192)  # run circuit with 8192 shots
print(counts)
```

The first line runs the circuit using 8192 shots. A shot is a single run of the circuit, the outcome of which will be a single basis state, e.g. 00, 01, 10, or 11. By running the circuit for several shots we can estimate the probability of measuring each of the basis states. This is similar to flipping a coin many times to find the probability of getting heads or tails. We choose to set the number of shots to be 8192, chosen because it is a power of 2. The second line prints the result of the simulation. In just 12 lines of code, we have simulated our first quantum circuit!

Rather than simply looking at the printed results, it is a lot better to plot them and visually see the results. To do this, we first want to convert the number of counts into the estimates of the probabilities for measuring that state, which can be done with the following lines

```{code} python
probs = {}
for key, value in counts.items():
    probs[key] = value/8192
print(probs)
```

We can then use pyplot to plot a bar chart with the outcomes
```{code} python
from matplotlib import pyplot as plt

fig, ax = plt.subplots()
bars = ax.bar(probs.keys(), probs.values(), linewidth=1)
ax.bar_label(bars)
plt.ylabel("probability")
plt.savefig("BellState_simulator.pdf",format="pdf",bbox_inches="tight")
plt.show()
```

This produces the plot shown in {numref}`fig:bell_state_qiskit_circuit`(b). Note that we do not get perfect 50\% probability because we only measured 8192 times. As we increase the number of shots, the probabilities will approach 50\%.

```{figure} ../images/BellState_IBM.pdf
---
name: fig:bell_state_real
width: 60%
align: center
---

Histogram of the measurement outcomes for the quantum circuit run on the IBM quantum computer. On the x-axis are the different basis states that were measured. The y-axis shows the inferred probability of measuring each of the basis states.
``` 

We can also run our quantum circuit on a real quantum computer provided by IBM. To do this, we first need to set up an IBM quantum experience account and to save our account in python using qiskit. See https://auth.quantum-computing.ibm.com for more details on how to sign up and how to save your account in python using a token. You will then need to install the pennylane package for interacting with qiskit, which will allow us to use IBM's devices. To install qiskit and the pennylane add-on we can use pip
```python
 >> pip install qiskit
 >> pip install pennylane-qiskit
```

Once this is set up, all you need to do is replace line 2 with the following
```{code} python
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService(channel="ibm_quantum", instance="ibm-q/open/main")
backend = service.backend("ibm_brisbane")

dev = qml.device('qiskit.remote', wires=2, backend=backend)
```

The first line loads the necessary functions from qiskit. The second line loads your IBMQ credentials that you have already saved in python by following the instructions on the IBMQ website. The third line loads the IBM device codenamed `ibmq_brisbane`. This device refers to a real quantum computer hosted by IBM, of which there are several. Using the online interface you can see which quantum computers are available to you and how many jobs have been submitted and are currently in the queue. You may want to use a device that is less busy. Note you will also want to change the name of the pdf that you save to file when running on the real device.

Running the code again with those changes runs your code on the quantum computer, which will take significantly longer than the simulator. This is because the data has to be communicated with the IBM server, placed in a queue to run on the device, and then finally run. When plotting the histogram of the measurements, you find something closer to {numref}`fig:bell_state_real`. Not only are the values further away from 50\% but we also have non-zero probabilities for the states 01, 10. This is because the current devices are not yet perfect and suffer from a significant amount of noise that affects the measurement results. Regardless, we have run our first circuit on a quantum computer and realised an entangled quantum state.

````{admonition} Exercises 2.3

**1.** Modify the BellState example code to implement the following circuit

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

* [BellState-simulator](../downloads/BellState-simulator.ipynb) - running the Bell state circuit on a simulator.
* [BellState-IBMQ](../downloads/BellState-IBMQ.ipynb) - running the Bell state circuit on a real IBM quantum computer.

Here is the notebook to setup and save your IBM credentials:

* [IBM_setup](../downloads/IBM_setup.ipynb) - setting up and saving your IBM credentials.

You can also use this [environment yaml file](../downloads/environment.yml) to setup a conda environment to run these notebooks, and all the code in this course.

````



---

## References
```{bibliography}
:filter: docname in docnames
```