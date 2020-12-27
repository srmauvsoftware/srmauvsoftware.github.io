---
layout: default
---

## Underwater Object Detection using Deep Learning

[[back]](./)

![](/assets/img/blog/inference.jpg)

Underwater object detection is one of the most fundamental tasks of an Autonomous Underwater Vehicle (AUV). The problem statement includes detecting a gate-shaped structured inside the water and navigating through its middle. 

For detecting the gate, we found that basic image processing techniques like RGB, HSV or Lab colorspace thresholding did not work well (even with histogram equlizations) because of changes in lighting conditions inside the water during different times of the day. So we tried using deep CNN architectures for detecting and classifying the pipes of the gate.

## Solution  
I tried using Convolutional Neural Networks for the task. Generally hugh data and training time is required to train CNNs from scratch to get perfect results which is not possible in the above task in which generating a large dataset containing Gate images is a hard job and traning on high end GPUs is very expensive. Workaround over this is Transfer Learning, in short - remove the last fully-connected layer of a pre-trained CNN (this layer’s outputs are the 1000 class scores for a different task like ImageNet), then treat the rest of the ConvNet as a fixed feature extractor for the new dataset. Further fine-tune the weights of the pretrained network by continuing the backpropagation on the Gate's dataset.
Amazing referece for Transfer Learning - [CS231n](http://cs231n.github.io/transfer-learning/)

TensorFlow Object Detection module provides trainable detection models in their [model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md). I chose Single Shot MultiBox Detectors(SSD) with MobileNet architecture.
Reason to choose the architecture - As our hardware is restricted to embedded systems, and dense deep learning models require high computational resources for inference, so i chose a comparatively less dense model which can infere a video feed to detect Gate on a Jetson TX1 with the other systems of the repository working parallely.
Reason to chose [Single Shot MultiBox Detectors](https://towardsdatascience.com/understanding-ssd-multibox-real-time-object-detection-in-deep-learning-495ef744fab) - Algorithm to draw bounding box on the object which needs to be detected.

## Procedure
1. Collected 140 images of gate from underwater recording, youtube videos.
2. Label the images using [LabelImg](https://github.com/tzutalin/labelImg) tool. It automatically generates the XML.
3. Split the data into test/train samples.
4. Generate TFRecords
5. Setup .config file for the SSD with custom hyperparameters
6. Export graph from trained model.
7. OpenCV optimization for inference on video feed.

## Dataset
![](/assets/img/blog/dataset.jpg)
Labelling the dataset for Single Shot Detectors-  
![](/assets/img/blog/labelling.png)

## Training
I trained the model on a Nvidia GTX 960m using CUDA. The tensorboard results -
![](/assets/img/blog/tensorboard.png)

## Inferece  
The results on the test data-set were -
![](/assets/img/blog/inference.jpg)

