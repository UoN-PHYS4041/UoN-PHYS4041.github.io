(sec-QML)=
# Quantum machine learning

In this section we will look at the application of variational quantum circuits applied to problems in machine learning. In particular, we will use them to solve the classical MNIST classification problem. This involves several steps:
1. representing images as quantum states, 
2. defining a quantum analogue of a neural network, and 
3. training the quantum neural network. 

The quantum neural network and its training will be similar to the variation quantum circuits we saw in previous sections. We will focus mainly on the new aspect: how to represent classical data as quantum states.


## Supervised learning: classification of MNIST

Before moving to our quantum approach to solving the MNIST classification problem, let us first recap on what this problem is. The MNIST handwritten digit dataset consists of 70,000, gray-scale $28 \times 28$ pixel images of handwritten digits (0-9), each with a label, which corresponds to the digit that the image represents. The task is to classify the images and to assign the correct digit to the image, which is a type of supervised learning task since the images are labelled. To simplify the problem, we can reduce the dataset to consider only a pair of digits (say, 3 and 6), in which case we have a binary classification problem. This task can be achieved with human levels of accuracy using a simple feed-forward neural network.

```{figure} ../images/MNISTDigits.png
---
name: fig:MNIST_digits
width: 100%
align: center
---

Examples from the MNIST handwritten digit dataset with the corresponding classification label.. 
``` 



Similarly, there is a second dataset called fashionMNIST, which has exactly the same format, but where the images correspond to 10 different items of clothing. Again the task is to classify the images according to the 10 categories, and can similarly be reduced to a binary classification task between pairs of categories.


```{figure} ../images/FashionMNIST.png
---
name: fig:sashion_MNIST
width: 100%
align: center
---

Examples from the fashion MNIST dataset with the corresponding classification label. 
```

