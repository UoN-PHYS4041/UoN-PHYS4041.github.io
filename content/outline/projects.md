# Assessment 

The form of assessment for this module is
* Project and paper (as a group): 70%
* Presentation and discussion (individual): 30%
* **PAPER DEADLINE 4pm May 12th, 2026 (submit via Moodle)**
* **Presentation date TBD after papers submitted** 

The project should be written up in a two-column letter style, similar to Physical Review Letters, and should be a maximum of 3 pages long. The project will be written as a group, with each member of the group submitting a copy of the project, along with a statement of contribution. Ideally the statement of contribution should say "All group members contributed equally". If this is not the case, then the statement should detail the contributions of each group member.


## Project template

The project should be written in the style of a scientific paper using LaTeX and the `revtex` package. You can use the [APS template](../downloads/Latex%20template.zip) to get started. As a guide, the paper should have (but is not restricted to) the following elements / structure:
1. Title
2. Author list
3. Abstract
4. Introduction
5. Setup of the problem
6. Results and discussion
7. Conclusion and outlook
8. Bibliography


## How the projects work

Below are four projects to choose from. You'll submit your preferences on Moodle and be put into groups based on your choices. I will arrange group meetings in our normal timetabled slot. Choose the setting you find most interesting. Project 4 is particularly suited to students on the Quantum Science and Technology programme, but anyone can choose any project.

Each project has a **starter notebook**, organised around the four core elements below. It provides the code you need that isn't the point of the project, such as loading data, evaluating circuits efficiently, counting the cost and simulating noise, so you can spend your time on the physics. The key pieces, such as the gradient methods, are left for you to write, each with a check so you know when your code works.

### The core elements

Whatever project you choose, it should showcase the four core elements of the course. Every project therefore has four **main tasks**, one for each element, in the same order:

1. **Circuit design.** How the problem is encoded in qubits, and how the choice of circuit (its structure, depth and entanglement) affects the results.
2. **Measurement and uncertainty.** Every quantity measured on a quantum computer comes from a finite number of shots. Report results with error bars, explain where they come from, and work out how many shots you need.
3. **Optimisation and gradients.** Optimise parametrised circuits using the gradient methods from [Lecture 5](../lectures/lecture5.md) (finite difference, SPSA and parameter shift), and compare them at equal *cost*: count the circuit evaluations and shots, not just the iterations.
4. **Noise.** Compare your noiseless results with noisy simulations of a real device (a *fake backend*, as in the course notebooks), and discuss how and why noise changes them.

Each project also suggests **further investigations**, from which you should choose at least one, or propose your own.

### How the paper is assessed

Your paper is what is marked; your code and notebooks are not marked directly. Completing the main tasks carefully, with correct results and sensible uncertainties, is the foundation of a good mark. **Higher marks come from how far you push the investigation:** asking a clear question and answering it, going beyond the main tasks, and critical, well-supported analysis of your results, including their limitations. A good paper tells a story, rather than working through a checklist.

### Requirements for all projects

- **Real hardware (bonus).** Free access to real quantum computers is available (see [Running on real quantum computers](hardwareAccess.md)). Running some of your circuits on real hardware is **not required**, but it is encouraged and will be credited as going beyond the main tasks. It also makes for a richer discussion in your paper. If you do, record the backend name and job IDs, and plan ahead, since queues can be long.
- **Code.** Keep your code in a shared git repository (e.g. a private GitHub repository) and commit regularly, so that the history shows how the project developed. Submit a link to the repository with your paper. Long simulations can be run on the HPC (see [Setting up Python and Qiskit](pythonEnvironment.md)).
- **Use of AI.** See [the AI statement](AIStatement.md). Any use of AI tools for code must be acknowledged. In your individual presentation you will be asked to walk through and explain parts of your group's code, and to predict what would happen if it were changed, so make sure you understand all of it.


### Project 1: Variational quantum eigensolver for molecules

In this project you will implement and test the variational quantum eigensolver (VQE). VQE was first demonstrated in the context of quantum chemistry by finding the ground state of small molecules {cite}`Peruzzo2014`{cite}`Kandala2017`, and this is the setting we will also focus on. You will expand on the example in [Lecture 4](../lectures/lecture4.md) and [Code 4](../../notebooks/04-vqe.ipynb) by computing the energy of HeH$^+$ as a function of the separation $R$.

**Starter notebook:** [Project 1 starter](../../notebooks/projects/project1-vqe-starter.ipynb)

**Main tasks**
1. *Circuit design.* Use VQE to compute the potential energy curve $E(R)$ of HeH$^+$ over a range of $R$, and determine the equilibrium bond length. Investigate how the depth and structure of the ansatz affect the accuracy, compared with exact diagonalisation.
2. *Measurement and uncertainty.* Put error bars on your energies. How many shots are needed to reach chemical accuracy ($1.6\times10^{-3}$ Hartree)? How many different measurement settings does the Hamiltonian need?
3. *Optimisation and gradients.* Implement the three gradient methods and compare how much it costs each of them to reach a given accuracy. Investigate the effect of the initial parameters, including local minima.
4. *Noise.* Evaluate your optimised circuits on a noisy fake backend across your range of $R$. How does noise change the potential energy curve and the equilibrium bond length?

**Further investigation**
1. Design and compare different ansatz circuits. Which features of the ground state does a good ansatz need to capture?
2. Apply VQE to a different (larger) molecule or model Hamiltonian from the literature.
3. Perform the whole optimisation on the noisy simulator, and/or apply error mitigation (see Project 4).
4. Run your circuits on real hardware (bonus).


### Project 2: Quantum machine learning classification

In this project you will perform and test quantum machine learning on a classification task. This builds on the MNIST classification example in [Lecture 6](../lectures/lecture6.md) and [Code 5](../../notebooks/05-mnist-qnn.ipynb).

**Starter notebook:** [Project 2 starter](../../notebooks/projects/project2-qml-starter.ipynb)

**Main tasks**
1. *Circuit design.* Perform binary classification on a pair of classes of your choice (other than 0 and 1). Implement at least one encoding of the images besides the one from the lecture (e.g. a different reduction of the image, amplitude encoding or FRQI), and investigate the depth and structure of the trainable circuit.
2. *Measurement and uncertainty.* Put error bars on your accuracies. How does training and testing with a finite number of shots, rather than exact expectation values, affect the results? How many shots are needed?
3. *Optimisation and gradients.* Compare the gradient methods at equal cost in circuit evaluations. Compare the training and test accuracy, and discuss which pairs of classes are hard and why.
4. *Noise.* Evaluate your trained classifier on a noisy fake backend. How much does noise reduce the accuracy, and which circuits are most robust?

**Further investigation**
1. Perform binary classification on Fashion-MNIST.
2. Implement multi-class classification.
3. Compare against a classical model with a similar number of parameters.
4. Train on the noisy simulator, and/or evaluate on real hardware (bonus).


### Project 3: Quantum neural network architectures

Classical neural networks owe much of their success to their architecture, for example convolutional layers for images. In this project you will build and test more advanced quantum neural network architectures for image data, working from the original research papers. There is no ready-made code for this project. You will need to design and implement the circuits yourselves in Qiskit.

The starting point is the *quanvolutional* neural network {cite}`Henderson2020`. A small parametrised quantum circuit acts like a convolutional filter: it is applied to each small patch (e.g. $2\times2$ pixels) of an image, and its measured expectation values form new "feature maps". These are then passed to a classical neural network.

**Starter notebook:** [Project 3 starter](../../notebooks/projects/project3-qnn-architectures-starter.ipynb)

**Main tasks**
1. *Circuit design.* Implement a quanvolutional layer and train a classical network on its feature maps for multi-class classification. Investigate the choice of quantum filter (random vs. structured circuits, number of qubits, entanglement), and compare against classical baselines: the same network on the original images, and with a classical convolutional layer.
2. *Measurement and uncertainty.* Put error bars on your accuracies. How do feature maps computed from a finite number of shots affect the classification? How many shots are needed?
3. *Optimisation and gradients.* Make the quantum filter trainable, using the gradient methods from Lecture 5, and compare trained filters with fixed (e.g. random) ones at equal cost.
4. *Noise.* Compute feature maps on a noisy fake backend. How does noise change them, and how robust is the classifier?

**Further investigation**
1. Use quantum circuits to *generate* images: implement a patch-based quantum generator trained adversarially against a classical discriminator (a quantum GAN) {cite}`Huang2021`.
2. Implement a fully quantum convolutional neural network (QCNN) {cite}`Cong2019`.
3. Repeat for Fashion-MNIST.
4. Run quantum filters on real hardware (bonus).


### Project 4: Noise, characterisation and error mitigation

Today's quantum computers are noisy, and understanding and reducing the effects of this noise is one of the central challenges of the field. In this project you will run experiments on realistic noisy simulations of real devices (and, ideally, on the real devices themselves), characterise how and why the results differ from the ideal predictions, and implement *error mitigation* techniques to recover more accurate results {cite}`Temme2017`{cite}`Kandala2019`.

**Starter notebook:** [Project 4 starter](../../notebooks/projects/project4-noise-mitigation-starter.ipynb)

**Main tasks**
1. *Circuit design.* Prepare GHZ states $\frac{1}{\sqrt2}(|0\cdots0\rangle + |1\cdots1\rangle)$ of increasing numbers of qubits on a chain of qubits of a (fake) device. Investigate how the circuit and its transpilation onto the device (layout, native gates, depth) affect the results.
2. *Measurement and uncertainty.* Use measurements in different bases to estimate the GHZ state fidelity, with error bars, as a function of the number of qubits. Characterise the readout errors of your qubits, implement readout error mitigation, and quantify the improvement.
3. *Optimisation and gradients.* Optimise a parametrised circuit on the noisy simulator, e.g. the parametrised GHZ preparation in the starter notebook, maximising the fidelity measured with your own estimator. Compare the gradient methods: which is most robust to noise and shot noise at a given cost?
4. *Noise.* Implement zero-noise extrapolation {cite}`Temme2017`{cite}`GiurgicaTiron2020` yourselves: amplify the noise by "folding" gates (e.g. replacing CNOT with CNOT·CNOT·CNOT), then extrapolate your measured observables back to zero noise. Relate your results to the device's reported error rates ($T_1$, $T_2$, gate and readout errors).

**Further investigation**
1. Repeat your experiments on real hardware (bonus), and compare different devices or platforms, e.g. IBM and Quantum Inspire, or different qubit layouts.
2. Measure $T_1$ and $T_2$ of a qubit yourselves, using circuits with delays, and compare with the reported values.
3. Combine readout mitigation and zero-noise extrapolation in your optimisation from task 3. Does mitigation help the optimiser find better parameters?
4. Investigate how the transpiler optimisation level, or dynamical decoupling, affects the results.
