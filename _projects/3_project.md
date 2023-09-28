---
layout: page
title: RSNA Breast Cancer Detection
description: Detecting breast cancer via mammographic imaging
img: assets/img/rsna_pic_1.png
importance: 1
category: work
related_publications: 
---

As part of a [Kaggle](https://www.kaggle.com/) hosted by the Radiological Society of North America my peers and myself contributed to help attemp solve the problem of detecting breast cancer through mammographic images. The approach we took can naively be broken up into 4 sections:

<ol>
    <li>loading in the images provided</li>
    <li>handling class imbalance</li>
    <li>training the model</li>
    <li>analyzing the performance of the model</li>
</ol>

### Loading in the Images 

The images were provided in a DICOM (Digital Imaging and Communications in Medicine) format, which is the standard in medical imaging. Initally we wanted to convert the image to a more manageable and manipuatble type, this could been done by convereting the image to a 2d array where each index (pixel) represents the brightness on [0,255]. We did not need to consider rgb values since the equipment used for imaging was strictly grayscale. Once the images were converted, we would need to crop the images the be the same size and to only contain the Area of Interest (AOI). The images needed to be the same size so we could pass them into the network seamlessly.

Given that the data set contained 54,723 images (314.72 GB) we quickly disocvered that our plan would not work as efficiently as we needed. The ammount of time required to process all these images was unexpected and not practical. Pivoting from our inital plan we researched and found a kaggle dataset from a user who had already done all the required resizing and cropping to provide contestents with. We opted to use these images.

### Handling Class Imbalance

After some brief exploration of the data we realized that there was a large discepancy between the number of cancerous and non-cancerous examples. Approxiametley 98% of the dataset were non cancerous and a mere 2% were cancerous examples.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_2.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


#### Image Augmentation with Undersampling

One apaproach to combat this class imbalance we used existing canerous ammograms to help populate the dataset. This was done via PyTorch Transforamtions. For each example we imposed random rotations up to 60 degrees, and random horizontal and vertial flipping. 

<div class="row">
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

We were still not convinced that with even after the augmentation this imbalance will yeild optimal performace from the model. The last step we took what to undersample where we used every cancerous example and undersampled the non-cancouers examples for a 50/50 split in training data.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/rsna_5.png" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>


### Training The Model
