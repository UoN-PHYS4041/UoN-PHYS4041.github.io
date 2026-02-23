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
  - python=3.12
  - pip
  - numpy
  - ipykernel
  - matplotlib
  - pip:
    - qiskit
    - qiskit-ibm-runtime
    - qiskit[visulization]
    - pennylane~=0.43.2
    - pennylane-qiskit
    - torch
    - torchvision
    - jax~=0.6.0
    - jaxlib~=0.6.0
    - optax
    - tensorflow
    - tensorflow-datasets
```

To create the conda environment, open a terminal in the directory where you saved the `environment.yml` file. Then run the following command.

```bash
conda env create -f environment.yml
```

This will create a new conda environment called `phys4041` with all the packages you will need for this course. You can activate the environment by running the following command.

```bash
conda activate phys4041
```

This environment can also be used for running Jupyter notebooks.


<!-- ## Setting up IBM account

In this course we will be writing code that could be run on the IBM quantum computers. We will be mostly using simulators that run on your personal computer. There will be no requirement to run on the actual devices. However, if you would like to run on the IBM quantum computers, we need to do a bit of setup.

First, you need to set up an account on the IBM Quantum Experience. You can do this by going to the [IBM Quantum Experience website](https://quantum-computing.ibm.com/). You will need to create an account, and then you will be able to access the quantum computers and simulators. This free account will give a very limited amount of runtime on the quantum computers (10 minutes per month), so use this sparingly.

Once you have an IBM quantum account, we need to save your credentials on your computer so that Pennylane can access the IBM quantum computers. This is done via a personal access token, as can be seen in {numref}`fig:IBMQuantum`. You can save this token to your computer by running the following python code.

```{figure} ../images/IBMQuantumInitialization.png
---
name: fig:IBMQuantum
width: 75%
align: center
---

IBM Quantum Experience website. At the top of the page is your personal access token, along with python code to save it to your computer.
``` 

You can save this token to your computer by running the following python code.

```python
from qiskit_ibm_runtime import QiskitRuntimeService

QiskitRuntimeService.save_account(  
    channel='ibm_quantum', 
    instance='ibm-q/open/main', 
    token='###########')
```

where you need to inlcude your personal token. You will only need to do this once, and then Pennylane will be able to access the IBM quantum computers.


## Using a real IBM quantum computer

In order to use a real IBM quantum device, we need to setup up a `backend` and a `device` in Pennylane. This is done by running the following code.

```python
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService(channel="ibm_quantum", instance="ibm-q/open/main")
backend = service.backend("ibm_brisbane")

dev = qml.device('qiskit.remote', wires=2, backend=backend)
```

This will use your previously saved access token to access the IBM servers. `ibm_brisbane` is one of the IBM quantum computers. You should use the [IBM Quantum Experience website](https://quantum-computing.ibm.com/) to find out which devices you currently have access to, and how busy they currently are.

````{note}
In this course, there is no requirement to use the real quantum computers, particularly because this tends to be a very slow process (minutes to hours) as you are sharing the quantum computer with many other users. Instead we will mostly use the simulators that run on your personal computer. For this you can set up the `device` as follows

```python
import pennylane as qml
dev = qml.device("default.qubit", wires=2)
```

Even if you want to run on a real device, use this simulated device first to test your code, as it will be much faster. You can then simply change your device and leave the rest of your code the same to run on the real device.
```` -->