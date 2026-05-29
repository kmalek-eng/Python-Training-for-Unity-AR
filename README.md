# Python Training for Unity AR

This repository contains Python implementations used to train, evaluate, and export deep learning models for deployment in Unity-based augmented reality applications.

## Repository Structure

### LeNet_MNIST_Training

LeNet-5 implementation for handwritten digit classification using the MNIST dataset.

Includes:

* Model training
* Model testing
* Accuracy evaluation
* Confusion matrix and ROC analysis
* Export of trained weights
* Conversion of weights to C# format

### Crack_Detection_Training

Crack classification models based on the ImmerseNet architecture.

Includes:

* Dataset preprocessing
* Model training
* Model evaluation
* Weight export to C# format

Two model configurations are provided:

* 32×32 input version
* 227×227 input version

### Crack_Segmentation

U-Net based crack segmentation framework.

Includes:

* Training using image-mask pairs
* K-fold cross-validation
* Model evaluation
* Segmentation result generation
* Checkpoint generation
* Conversion of trained weights to C# format

### Archive

Contains older experimental scripts retained for reference.

## Related Repository

The Unity deployment project is maintained separately:

https://github.com/kmalek-eng/LeNetAR

## Workflow

1. Train and evaluate a model in Python.
2. Export the trained weights.
3. Convert the weights to a C#-compatible format.
4. Import the exported weights into the Unity project.

## Requirements

Refer to the README inside each project folder for dataset requirements, dependencies, execution instructions, and generated outputs.

## Citation

If you use this code, please cite the author:

Kaveh Malek
