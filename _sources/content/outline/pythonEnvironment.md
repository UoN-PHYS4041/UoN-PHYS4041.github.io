# Setup Python and Pennylane

In this course we will be using Python as our programming language. To run code on quantum computers (and simulators), we will be using the Python library [Pennylane](https://pennylane.ai/install), and the [qiskit plugin for Pennylane](https://docs.pennylane.ai/projects/qiskit/en/latest/). While you are free to setup and install python and these packages in your preferred way, here I provide details of how to setup everything you will need for this course using a Conda environment.


## Installing Conda

Conda is a package manager that can be used to install python and other packages. It also allows you to create virtual environments so that you can have different versions of python and different packages installed on your computer without them interfering with each other. If you do not already have Conda installed, you can install it from the [Anaconda website](https://docs.anaconda.com/anaconda/install/).


## Creating a Conda environment

To create a new Conda environment, with all the packages you will need for this course, you can use [this yaml file](../downloads/environment.yml). You can download this file and save it to your computer. The contents of this file are as follows.

```yaml
# File name: environment.yml

name: phys4041
channels:
  - conda-forge
  - defaults
dependencies:
  - python=3.10
  - pip
  - numpy
  - ipykernel
  - matplotlib
  - pennylane
  - pip:
    - qiskit
    - qiskit-ibm-runtime
    - qiskit[visulization]
    - pennylane-qiskit
```