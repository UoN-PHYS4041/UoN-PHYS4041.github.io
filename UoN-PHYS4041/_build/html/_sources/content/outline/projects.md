# Assessment 

The form of assessment for this module is
* Project and paper (as a group): 70%
* Presentation and discussion (individual): 30%
* **PAPER DEADLINE 4pm May 13th, 2025 (submit via Moodle)**
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


## Project Choices

Below are several possible projects for you to choose from, which you will be able to submit on Moodle. Based on your choices you will be put into groups. I will arrange group meetings with you to happen in our normal 11am slot on Tuesdays. All of the projects include the same steps but in different settings. Whatever project you choose, you will be tested on the same skills, but you may find a particular setting more interesting.

Note: it would be great to have something run on the real IBM devices. However, I am aware that running on the quantum computers takes a significant amount of time and can be a bit problematic. Since it amounts to a change of a few lines of code you will not be marked down if this can’t be achieved during the project. With that said, performing such experiments provides a natural chance to discuss the realities of working with current noisy quantum computers (but you could introduce this anyway), which can lead to a stronger discussion in your paper.


### Project 1: Variational quantum eigensolver for molecules

In this project we will implement and test the Variational Quantum Eigensolver (VQE) algorithm. The VQE algorithm was first demonstrated in the context of quantum chemistry by finding the ground state of simple molecules{cite}`Peruzzo2014`{cite}`Kandala2017`. This is the case we will also focus on. We will expand on the example given in the lecture notes by finding the ground state for different molecular separations to find the lowest energy separation.

**Main tasks**
1. Solve the H-He+ molecule from lecture notes but with different separation values
R (corresponding to different coefficients in the Hamiltonian)
2. Perform and compare VQE optimisation with the different methods for computing
(or approximating) gradients
3. Investigate changing the depth of the VQE ansatz

**Further investigation**
1. Consider different VQE ansatz circuits and compare
2. Perform VQE for different (more complicated) molecules
3. Run optimised circuits on IBM devices
4. Perform optimisation process on IBM device



### Project 2: Quantum machine learning classification

In this project we will perform and test quantum machine learning on the task of classification. This will build on the code on Moodle for the MNIST handwritten digit classification.

**Main tasks**
1. Modify code to perform binary classification on different pairs of digits.
2. Implement different methods for encoding the image data (using simulator)
3. Test and compare different depths for the ansatz circuit
4. Compare different methods for computing (or approximating) gradients

**Further investigation**
1. Perform binary classification using fashion MNIST
2. Implement multi-class classification
3. Run an optimised circuit on IBM device


### Project 3: Quantum generative adversarial network

In this project we will create a quantum circuit to generate handwritten digits, trained using the MNIST dataset. The approach for generating these images will be to use Generative Adversarial Networks consisting of a quantum generator and a classical discriminator.

**Main tasks**
1. Write code to implement Quantum GAN, based on
https://pennylane.ai/qml/demos/tutorial_quantum_gans/
2. Investigate generating different digits
3. Investigate different methods for patching
4. Investigate different circuit structures
5. Compare different methods for computing (or approximating) gradients

**Further investigation**
1. Generate Fashion MNIST instead of MNIST handwritten digits.
2. Run an optimised circuit on IBM device


### Project 4: Quantum Convolutional Neural Networks

In this project we classify MNIST handwritten digits using a quantum version of convolutional neural networks. This consists of a quantum convolutional layer, followed by a classical dense neural network

**Main tasks**
1. Write code to implement Quantum Convolutional Network, based on
https://pennylane.ai/qml/demos/tutorial_quanvolution/
2. Test with multi-class classification
3. Compare against comparable classical neural network
4. Compare different methods for computing (or approximating) gradients

**Further investigation**
1. Classify Fashion MNIST instead of MNIST handwritten digits.
2. Run an optimised circuit on IBM device




---

## References
```{bibliography}
:filter: docname in docnames
```