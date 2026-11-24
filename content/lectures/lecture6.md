---
numbering:
  title:
    enabled: true
    offset: 0
  headings: true
  figure:
    enumerator: '6.%s'
  equation:
    enumerator: '6.%s'
---
(sec-QML)=
# Quantum machine learning

In this section we will look at the application of variational quantum circuits applied to problems in machine learning. In particular, we will use them to solve the classical MNIST classification problem. This involves several steps:
1. representing images as quantum states, 
2. defining a quantum analogue of a neural network, and 
3. training the quantum neural network. 

The quantum neural network and its training will be similar to the variational quantum circuits we saw in previous sections. We will focus mainly on the new aspect: how to represent classical data as quantum states.


## Supervised learning: classification of MNIST

Before moving to our quantum approach to solving the MNIST classification problem, let us first recap on what this problem is. The MNIST handwritten digit dataset consists of 70,000, gray-scale $28 \times 28$ pixel images of handwritten digits (0-9), each with a label, which corresponds to the digit that the image represents. The task is to classify the images and to assign the correct digit to the image, which is a type of supervised learning task since the images are labelled. To simplify the problem, we can reduce the dataset to consider only a pair of digits (say, 3 and 6), in which case we have a binary classification problem. This task can be achieved with human levels of accuracy using a simple feed-forward neural network.

```{figure} ../images/MNISTDigits.png
---
name: fig:MNIST_digits
width: 100%
align: center
---

Examples from the MNIST handwritten digit dataset with the corresponding classification label.
``` 



Similarly, there is a second dataset called fashionMNIST, which has exactly the same format, but where the images correspond to 10 different items of clothing. Again the task is to classify the images according to the 10 categories, and can similarly be reduced to a binary classification task between pairs of categories.


```{figure} ../images/FashionMNIST.png
---
name: fig:fashion_MNIST
width: 100%
align: center
---

Examples from the fashion MNIST dataset with the corresponding classification label. 
```


## Classical data as quantum states

The first task in devising a quantum approach to this type of machine learning task is to represent the images as a quantum state, or equivalently, as a quantum circuit[^1]. This quantum state will then be the initial state for a variational quantum circuit, the measurement of which will allow us to classify the image, as described in {numref}`sec-quantum-NN`.

[^1]: Actually this is only one approach. There are other approaches where the images are directly inputted as angles into a variational quantum circuit. What we are considering can be considered as a subset of these approaches.

### Basis and angle encoding

The most direct way to encode an image is to assign a qubit to each of the pixels of the image. For a black and white image we can identify the zero state $|0\rangle$ with a white pixel, and $|1\rangle$ with a black pixel. This is called *basis encoding*. We can also represent gray-scale values by making use of qubit rotations, which is called *angle encoding*. If we scale the pixel values such that 0 correspond to pure white and 1 to pure black, then for a pixel value $x\in [0,1]$, we can rotate the qubit using the Pauli-Y rotation gate, as so $R_Y(\pi x) |0\rangle = \cos(\pi x/2) |0\rangle + \sin(\pi x/2) |1\rangle$. If we measure the qubit then we can extract the original pixel value from $x = 2\arccos(\sqrt{p(|0\rangle)}) /\pi$. This provides a one-to-one mapping between our pixels and qubit states. 

While this approach is very simple and natural, even these very small $28\times 28$ images will require 784 qubits to represent the image. Current devices have of order 100 qubits (e.g. 156 on IBM's Heron processors and 105 on Google's Willow), and these are still too noisy to run deep circuits on all of them. So this approach is not yet realistic for the full image. We could scale the image down further to use this approach, but in the following sections we will show alternative methods of representing the full images using far fewer qubits.


### Amplitude encoding

The power of quantum computing comes from the fact the quantum states can exist in superposition states and the dimensionality of the Hilbert space is exponential in the number of qubits. In other words, to fully specify the quantum state of $N$ qubits, there are $2^N$ complex numbers: the probability amplitudes for each of the basis states. Amplitude encoding takes advantage of this by encoding the image in these probability amplitudes. This means, to represent an image with $M$ pixels, we need $\lceil \log_2 M \rceil$ qubits, and our $28 \times 28$ pixel images can be encoded in only 10 qubits. To encode the image, we take the value of the $i^\text{th}$ pixel, which we label $x_i$ and normalise it to become $y_i = x_i/\sqrt{\sum_j x^2_j}$. The amplitude encoding can then be written as 

$$
|\psi\rangle = \sum_i y_i |i\rangle,
$$

where $|i\rangle$ is the basis state which is the binary representation of $i$. In this representation we can arbitrarily choose how to label the pixels (and different choices may lead to different results). To measure the images, we simply measure the probability of measuring each of the bitstrings, and then $y_i = \sqrt{p(|i\rangle)}$.

This encoding has the benefit of requiring far fewer qubits than basis encoding, which makes it more feasible on near-term devices. However, this approach has two downsides. The first is that while we have reduced the number of qubits, in order to create this state generally requires a circuit that scales exponentially with the number of qubits. This is not a surprise, since exponential in the number of qubits means linear in the number of pixels. We don't get anything for free, we have traded qubit number for circuit depth. The second downside is that we have had to normalise our pixel values, which was not the case in basis encoding. While we can still reproduce our images, we have actually thrown away some information about the overall scale of the pixel values, or in other words, the contrast of the image. Many images with different contrast will have exactly the same quantum state representation. Furthermore, if we have an image with a lot of black, then the apparent contrast of the normalised image will be much lower, as shown in {numref}`fig:amplitude_encoding`. First, we note the different colour scale in these figures. Second, the darker image appears relatively more washed out when normalised. Since we will be directly comparing these images, this could cause issues for classification and is not a favourable property.


```{figure} ../images/AmplitudeEncoding.png
---
name: fig:amplitude_encoding
width: 100%
align: center
---

The effects of normalising the images for the amplitude encoding on two example images for fashionMNIST.
```


### FRQI

Let us consider one final method for encoding images as quantum states, called the flexible representation of quantum images (FRQI){cite}`FRQI`. This combines the benefit of the amplitude encoding, using a logarithmic number of qubits, without the need to normalise the pixel values and lose information about the image contrast. The idea is to use $\lceil \log_2 M \rceil$ qubits to label the position of the pixel, as in the amplitude encoding, but to introduce an additional qubit which encodes the value of that pixel, in the same way as the angle encoding. By entangling these sets of qubits, we can represent the image as follows

$$
|\psi\rangle = \frac{1}{\sqrt{M}}\sum_i |i\rangle \otimes ( \cos(\pi x_i/2) |0\rangle + \sin(\pi x_i /2) |1\rangle ).
$$

To understand what this means, consider the simplest case of a two-pixel image. The image would then be encoded as

$$
\begin{aligned}
|\psi\rangle &= \frac{1}{\sqrt{2}}\Big[|0\rangle \otimes ( \cos(\pi x_0/2) |0\rangle + \sin(\pi x_0 /2) |1\rangle ) + |1\rangle \otimes ( \cos(\pi x_1/2) |0\rangle + \sin(\pi x_1 /2) |1\rangle )\Big]\\
&= \frac{1}{\sqrt{2}}\Big[\cos(\pi x_0/2) |00\rangle + \sin(\pi x_0/2) |01\rangle + \cos(\pi x_1/2) |10\rangle + \sin(\pi x_1/2) |11\rangle\Big].
\end{aligned}
$$

We have created an entangled state between the "label" qubits and the "value" qubit.

With this quantum state representation of the image, we can represent the $28\times 28$ MNIST images, without throwing away any information, using only 11 qubits.



````{admonition} Exercises 6.1

**1.** Consider the super simple image consisting of two pixels, which can classically be written as the column vector

$$
\left(\begin{matrix} 
    x_0 \\
    x_1
    \end{matrix}\right).
$$

Write down the column vector corresponding to the quantum state using: (i) angle encoding, (ii) amplitude encoding, (iii) FRQI.

````


(sec-quantum-NN)=
## Quantum neural network

We now have a quantum version of our dataset using one of the above encoding methods. Next we want to introduce a quantum circuit analogue of the neural network in order to classify the data. This is simply a parametrised quantum circuit that we apply to our quantum data, followed by a measurement that we interpret as a classification label. As an example, let us consider a simple case where we have a binary classification problem with data on four qubits, we could then use a circuit of the form

```{figure} ../images/QuantumNN.png
---
name: fig:quantum_nn
width: 75%
align: center
---
```


Here we follow the circuit for encoding the image as a quantum state with a circuit that has 6 parameters $\theta_i$, and a measurement on the first qubit. We can classify the image based on which of the measurement outcomes, $|0\rangle$ or $|1\rangle$ has the largest probability. Our goal is then to optimise the angles $\theta_i$, so that the classification is as accurate as possible over the entire dataset, just as in standard machine learning. 

We can even use the same cost functions. If we take $p(|0\rangle)$ as the output, then this is a value in the interval $[0, 1]$, and we can map our two classes to the labels $0$ and $1$. For example, for a given image labelled $i$, let us write the measured probability for that image as $x_i$ and let $y_i$ be the actual label for that image, then the mean squared error loss function for a batch of images would be

$$
\frac{1}{N}\sum_{i=1}^N (x_i - y_i)^2.
$$

Using the methods from {numref}`sec-gradient`, we can differentiate this loss function to perform gradient descent, just like we do for neural networks.

Just as the success of classical neural networks depends on the particular structure (e.g., number of parameters, number hidden layers, convolutional layers, etc.), we must also design our quantum circuits in a way to most effectively classify the data. This is an area of active research. Since the quantum circuit is just a function of its parameters, we can also use all of our familiar machine-learning tools, such as mini-batches and the Adam optimiser, to train it.



## MNIST classification example

As a concrete example, let us consider the task of classifying the digits 0 and 1 from the MNIST handwritten digits dataset. We will use the simple angle encoding for this example. However, we cannot use as many qubits as pixels since it is far too many to simulate. Instead we will first scale the images down to 9 by 9 and then take only the central row of the image. In this way we reduce the number of qubits needed from 784 to 9. In {numref}`fig:mnist_reduced` we show an example of the image reduction for a 0 digit. 

```{figure} ../images/MNISTReduced.png
---
name: fig:mnist_reduced
width: 65%
align: center
---

Reduction of the MNIST digits before angle encoding.
```


For the quantum circuit we will use a variation of the circuit before that uses general rotation gates, namely


```{figure} ../images/MNISTExampleCircuit.png
---
name: fig:mnist_example_circuit
width: 70%
align: center
---
```

where this is a simplified circuit on only 4 qubits. The actual circuit will use 9 qubits. The initial $R_Y$ gates implement the angle encoding of our image. We can also increase the length of the circuit adding more layers of rotations and CNOTs. The gates labelled $U$ are general single qubit gates that implicitly depend on three parameters each. From the circuit we will compute the expectation value of $Z$ on the first qubit, i.e., $\langle Z_1 \rangle$. This is a number $f\in[-1,1]$, so we map the classification labels to $\pm 1$: in the code we use $y = +1$ for the digit 0 and $y=-1$ for the digit 1.

For the loss functions (and accuracy) we use the hinge loss

$$
    L = \frac{1}{N} \sum_{i=1}^N \max( 1- y_i f_i, 0),
$$

where $f_i = \langle Z_1\rangle$ is the output of the circuit for image $i$ in a batch of size $N$, and $y_i = \pm 1$ is its true label. A prediction is correct (and counts towards the accuracy) if $f_i$ and $y_i$ have the same sign. The loss is zero only if $y_i f_i \ge 1$, i.e. the prediction is correct *and* confident. Correct but unconfident predictions ($0 < y_i f_i < 1$) are still penalised a little, and wrong predictions are penalised more strongly.



```{figure} #fig-mnist-training
:name: fig:mnist_classification
:width: 95%
:align: center

Hinge loss (left) and accuracy (right) while training the quantum neural network to classify reduced MNIST 0 and 1 digits, for circuits with 1 and 2 layers. Dashed lines show the accuracy on unseen test images. Produced by [Code 5](../../notebooks/05-mnist-qnn.ipynb).
```

The code that implements this is in [Code 5: MNIST classification](../../notebooks/05-mnist-qnn.ipynb). The circuit is built in Qiskit with the pixel values and the weights as circuit parameters. The gradient of $\langle Z_1\rangle$ with respect to every weight is computed with the parameter-shift rule from {numref}`sec-parameter-shift`, and the weights are updated with the Adam optimiser using mini-batches of images. In {numref}`fig:mnist_classification` we show the loss and the accuracy during training, for circuits with one full set of rotations and CNOTs (which we call 1 layer) and with two full sets, one after another (2 layers). Both reach high accuracy on this easy task, but the extra depth and parameters of the 2-layer circuit give a lower loss and a higher accuracy ($\sim 97\%$ vs $\sim 99\%$ on the test set). The cost is that each training step needs $2P+1$ circuit evaluations per image for $P$ parameters, so the deeper circuit takes more than twice as long to train.


````{admonition} Exercises 6.2 

**1.** (Code) Modify the [Code 5: MNIST classification](../../notebooks/05-mnist-qnn.ipynb) code to classify digits 3 and 6 instead of 0 and 1. Is this task harder? Why might the central row of the image be a poor choice for these digits?

````
