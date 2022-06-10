### [Overview](#overview)  | [Steps](#steps) | [Notebooks](#notebooks) | [Results](#results) | [Student](#student) | [References](#references) 

# Face Extraction

This is the final project for the image processing course at the University of São Paulo (USP)

---
## Overview

In this project I did a sistem for sentiment classification in vídeos.

In order to do this I did the following steps:
 1. Image Feature Extraction - since we could not use CNN's we used SIFT descriptors in conjunction with k-means in order to generate the bag-of-visual-words.
 2. Face Extraction model - With the extracted features from the _Image Feature Extraction_ step, we use diferent types of models to create a bouding box around the face.

---
## Steps

In this section we will talk about each step that was done so that we could achieve our goal 

### **Image Feature Extraction**

<hr style="width:100%, height:1px">

In order to describe an image I use the SIFT descriptor. The SIFT descriptor will describe an image as a list of vectors. Since we need to create words that describe an image, we first use k-means model on the vectors generated by the SIFT descritor, this will cluster the vectors that describes the images to a cluster. Tha being said, the idea is that each cluster will be representing a bag-of-visual-word, and since each image is composed by multiple SIFT vectors, each SIFT vector will be "mapped" to a cluster wich is also visual-word. 

Now that an image is described as multiple visual-words, we just need to create the ocorrences that each visual-word occurs in the image, so I create a histogram of ocorrences of visual-words in a image. 

### **Face Extraction model part 1**

<hr style="width:100%, height:1px">

With the extracted features from the last step we use diferent types of models in order to extract the bounding box. That being said, we have to define wich models to use. Since the task is to predict multiple continuous values the models used are regression models.

In the interest of stablishing the best model for this task we calculate the mean of RMSE between each predicted vector and the values that we want to predict, we also display an histogram of each RMSE values.

<div class="row" style="clear: both; display: table;">
  <div class="column" style="float: left; max-width:30%; padding: 1%;text-align: center;">
    <h5 style="font-weight: 500;">Gradient bosting histogram</h5>
    <img src="https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/Face%20Extraction%20model%20part%201/gradientBosting.png" >
  </div>
  <div class="column" style="float: left; max-width:30%; padding: 1%;text-align: center;">
    <h5 style="font-weight: 500;">KNNr RSME histogram</h5>
    <img src="https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/Face%20Extraction%20model%20part%201/KNNr.png" >
  </div>
  <div class="column" style="float: left; max-width:30%; padding: 1%;text-align: center;">
    <h5 style="font-weight: 500;">Simple NN RSME histogram</h5>
    <img src="https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/Face%20Extraction%20model%20part%201/simpleNN.png">
  </div>
</div>

<div style="margin: auto, width: 50%, text-align: center;">
  <table>
    <tr>
      <th></th>
      <th>gradientBosting</th>
      <th>knnRegression</th>
      <th>SVGRegression</th>
      <th>simpleNN</th>
    </tr>
   <tr>
      <th>mean RSME</th>
      <th>64.5</th>
      <th>68.15</th>
      <th>3.607877e+12</th>
      <th>88.7</th>
    </tr>
  </table>
</div>

---
## Notebooks
Please see the following tutorial notebooks for a guide on how to use this project
 - **Image Feature Extraction** : [notebook](https://colab.research.google.com/drive/1J5B1rTAGaAfFelf8P9d4lXzjjH1j_WBr#scrollTo=rLKUJZz0eCGp)
 - **Face Extraction model part 1** : [notebook](https://colab.research.google.com/drive/14EdCEEEd_vQPIMs6hrZXmW4ZPI250XAt#scrollTo=GivtF11O1irD)

---
## Results

---
## Students
  - Victor Akihito Kamada Tomita | 10692082 | akihito012@usp.br
---
## References
  - [Wider Dataset](http://shuoyang1213.me/WIDERFACE/)
  - [SKLearn](https://scikit-learn.org/stable/modules/multiclass.html)
