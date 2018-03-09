---
title: Object Detection using Deep Learning
date: 2018-02-05 23:29:09
categories:
- Deep Learning
- Image Processing
tags:
- Image Processing
- Tensorflow
- Deep Learning
- SSD
- Object Classification
- Object Detection
- SRMAUV
---

The problem statement included a task to detect a Gate and different colored buoys placed underwater through which an AUV should pass. Basic image processing techniques like RGB, HSV or Lab colorspace filteration didn't work well even with histogram equlization due to change in color of gate due to different lightning conditions underwater. So I tried to implement Deep Learning to classify, detect and draw a bounding box over the pipes of the Gate.

## Solution  
I tried using Convolutional Neural Networks for the task. Generally hugh data and training time is required to train CNNs to get perfect results which is not possible in the above task in which generating Gate data is a hard job and traning on high end GPUs is not possible. Workaround over this is Transfer Learning, in short - remove the last fully-connected layer of a pre-trained CNN (this layer’s outputs are the 1000 class scores for a different task like ImageNet), then treat the rest of the ConvNet as a fixed feature extractor for the new dataset. Further fine-tune the weights of the pretrained network by continuing the backpropagation on the Gate's dataset.
Amazing referece for Transfer Learning - [CS231n](http://cs231n.github.io/transfer-learning/)

TensorFlow Object Detection module provides trainable detection models in their [model zoo](https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md). I chose Single Shot MultiBox Detectors(SSD) with MobileNet architecture.
Reason to chose MobileNet as the architecture - As our hardware is restricted to embedded systems, and dense deep learning models require high computational resources for inference, so i chose a comparatively less dense model which can infere a video feed to detect Gate on a Jetson TX1 with the control and other systems of the repository working parallely.
Reason to chose [Single Shot MultiBox](https://towardsdatascience.com/understanding-ssd-multibox-real-time-object-detection-in-deep-learning-495ef744fab) - Algorithm to draw bounding box on the object which needs to be detected.

## Procedure
1. Collected 140 images of gate from underwater recording, youtube videos.
2. Label the images using [LabelImg](https://github.com/tzutalin/labelImg) tool. It automatically generates the XML.
3. Split the data into test/train samples.
4. Generate TFRecords
5. Setup .config file for the SSD with custom hyperparameters
6. Export graph from trained model.
7. OpenCV optimization for inference on video feed.

## Dataset
<img src="/assets/images/deep_data/1.jpg" alt="1" width="1000" height="1000">
Labelling the dataset for Single Shot Detectors-  
<img src="/assets/images/deep_data/2.png" alt="2" width="1000" height="1000">

## Training
I trained the model on a Nvidia GTX 960m using CUDA. The tensorboard results -
<img src="/assets/images/deep_data/3.png" alt="3" width="1000" height="1000">

## Inferece  
The results on the test data-set were -
<img src="/assets/images/deep_data/4.jpg" alt="4" width="1000" height="1000">
