---
layout: page
title: RSNA Breast Cancer Detection
description: Attempting Detecting breast cancer via mammographic imaging
img: assets/img/rsna_pic_1.png
importance: 4
category: work
related_publications: 
---
[Kaggle Notebook](https://www.kaggle.com/code/jaketaranov/rsna-breast-cancer-detection)


As part of a [Kaggle](https://www.kaggle.com/) contest hosted by the Radiological Society of North America my peers and myself contributed to help attempt solve the problem of detecting breast cancer through mammographic images. The approach we took can naively be broken up into 4 sections:

<ol>
    <li>loading in the images </li>
    <li>handling class imbalance</li>
    <li>training the model</li>
    <li>analyzing model performance</li>
</ol>

### Loading in the Images 

The images were provided in a DICOM (Digital Imaging and Communications in Medicine) format, which is the standard in medical imaging. Initially we wanted to convert the image to a more manageable and manipulatable type, this could be done by converting the image to a 2d array where each index (pixel) represents the brightness on [0,255]. We did not need to consider rgb values since the equipment used for imaging was strictly grayscale. Once the images were converted, we would need to crop the images to be the same size and to only contain the Area of Interest (AOI). The images needed to be the same size so we could pass them into the neural network seamlessly.

Given that the data set contained 54,723 images (314.72 GB) we quickly discovered that our plan would not work as efficiently as we needed. The amount of time required to process all these images was unexpected and not practical. Pivoting from our initial plan we researched and found a kaggle dataset from a user who had already done all the required resizing and cropping to provide contestants with. We opted to use these images.


### Handling Class Imbalance

After some brief exploration of the data we realized that there was a large discrepancy between the number of cancerous and non-cancerous examples. Approximately 98% of the dataset were non cancerous and a mere 2% were cancerous examples.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


#### Image Augmentation with Undersampling

One approach to combat this class imbalance was to use existing cancerous mammograms to help populate the dataset. This was done via PyTorch Transformations. For each example we imposed random rotations up to 60 degrees, and random horizontal and vertical flipping. 

<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_3.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

With the augmentation applied our dataset became less unbalanced having 23% cancerous examples and 77% non cancerous.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_4.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

We were still not convinced that even after the augmentation this imbalance will yield optimal performance from the model. The last step we took was to undersample where we used every cancerous example and undersampled the non-cancerous examples for a 50/50 split in training data.

<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_5.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


### Training The Model

Due to the discrete and subliminal signs of cancer in the provided imaging, We knew that we needed to use a deep neural network (a neural network with many layers). The more layers we can instantiate in our model, the less [bias](https://en.wikipedia.org/wiki/Bias%E2%80%93variance_tradeoff) we hope to have and the more able it is to recognize these subtle characteristics of cancer in mammographic images.

The model we selected to use for the classifier was ResNet 18. Residual networks (ResNets) are a popular and effective technique used in image classification. It gained mass popularity around 2016 when it achieved 1st place at the ImageNet Large Scale Visual Recognition Challenge, and a subsequent [paper](https://arxiv.org/abs/1512.03385) was released shortly after.

The core of ResNet's power comes from its ability to solve the [vanishing gradient problem](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) by using skip connections. The vanishing gradient problem states that in gradient based deep neural networks, when we perform backpropagation the gradient will become vanishingly small, preventing updates of the weights. It is specifically the case when the gradients of the later layers (closer to the output) are less that one, the multiplication of these gradients will cause the updates from the earlier layers (closer to the input) to become functionally obsolete. Skip connections allow us to skip some of these layers that may contain small gradients that prevent us from updating our weights.


<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_6.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

Some more motivation to use this approach came from a [paper](https://onlinelibrary.wiley.com/doi/abs/10.1002/ima.22698) in 2022 that achieved an accuracy of ~98% for detecting breast cancer in histopathological images.


### Analyzing Model Performance
After experimenting with the data preparation and tuning [hyperparameters](https://en.wikipedia.org/wiki/Hyperparameter_(machine_learning)) we discovered that our model was not performing much better than a random classifier. Looking at the true and false positive rates through a Receiver Operating Characteristics (ROC) curve the performance became clear. Given an optimal ROC curve.


<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_7.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

In contrast to ours.

<div class="row" align="center">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_8.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

We can clearly see that ours is performing as a random classifier.

The general outcome of this Kaggle contest was not too promising. Many teams has results similar to ours where the winning team only had a [F1](https://en.wikipedia.org/wiki/F-score) score of 55. Where,

$$ F_1 = \frac{TP}{TP + \frac{1}{2}(FP + FN)} $$

Inherently it is a very difficult problem to solve, which I believe is due to the minute subtlety that signs of cancer may present itself in. While we did not achieve the result we expected or hoped for, it was still an immense learning experience. I was exposed to some new technologies and techniques while also getting to practice and refresh myself on some I may have not used in recent history.

