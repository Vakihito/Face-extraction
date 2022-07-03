### [Overview](#overview)  | [Steps](#steps) | [Notebooks](#notebooks) | [Results](#results) | [Points](#points) | [Student](#student) | [References](#references) 

---

# Face Extraction

This is the final project for the image processing course at the University of São Paulo (USP) 

OBS : This project was developed in google colab so make sure to use the colab enviroment, if you want to just test the final version please use this [notebook](https://colab.research.google.com/drive/1NWbl8s3NyF-bp_RvT3qyE_Q1HliV-uQK)

<p align="center">
   <img src="https://github.com/Vakihito/Face-extraction/blob/main/images/final%20result/V3/V3.gif?raw=true" width="150" />
</p>

---
## Overview

In this project I made a sistem for sentiment classification in vídeos.

In order to do this I did the following steps:
 1. **Image Feature Extraction** - since we could not use CNN's we used SIFT descriptors in conjunction with k-means in order to generate the bag-of-visual-words.
 2. **Face Extraction model** - With the extracted features from the _Image Feature Extraction_ step, we use diferent types of models to create a bouding box around the face.
 3. **Sentiment classification in faces** - After the face is extracted from the image, we need a sentiment classification model for faces. In order to do this we finetune the RESNEt-50 model in order to classify the sentiment in the face. Since the main task wa to extract the face from the image, we can finally use Conv Nets.
 4. **Sentiment classification in text** - Since a video is made out of 2 channels, voice and image, we need to extract the sentiment in the video, so we train a text classification model using the embeddings generated by an encoder, and then use a DNN for finetuning and classification.
 5. **Sentiment classification in videos** - With all the models trained for each modelity we can train a multimodel model for classification of the video. In order to demonstrate our model we created an aplication that gets a video from Youtube and performes each step for sentiment classification in videos. 

---
## **Steps**

In this section we will talk about each step that was done so that we could achieve our goal 

### **1 .Image Feature Extraction**

---

In order to describe an image I use the SIFT descriptor. The SIFT descriptor will describe an image as a list of vectors. Since we need to create words that describe an image, we first use k-means model on the vectors generated by the SIFT descritor, this will cluster the vectors that describes the images to a cluster. Tha being said, the idea is that each cluster will be representing a bag-of-visual-word, and since each image is composed by multiple SIFT vectors, each SIFT vector will be "mapped" to a cluster wich is also visual-word. 

Now that an image is described as multiple visual-words, we just need to create the ocorrences that each visual-word occurs in the image, so I create a histogram of ocorrences of visual-words in a image. The dataset that I used to traing the k-means model was the [Wider Dataset](http://shuoyang1213.me/WIDERFACE/).

### **2. Face Extraction model part 1**

<hr style="width:100%, height:1px">

With the extracted features from the last step we use diferent types of models in order to extract the bounding box. That being said, we have to define wich models to use. Since the task is to predict multiple continuous values the models used are regression models.

With the interest of stablishing the best model for this task we calculate the mean of RMSE between each predicted vector and the values that we want to predict, we also display an histogram of each RMSE values.

GradBost RSME histogram       |KNN RSME histogram         |simpleNN RSME histogram
:-------------------------:|:-------------------------:|:-------------------------:
![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/Face%20Extraction%20model%20part%201/gradientBosting.png) |  ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/Face%20Extraction%20model%20part%201/KNNr.png)  |  ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/Face%20Extraction%20model%20part%201/simpleNN.png)


| metric        | gradientBosting  | knnRegression | SVGRegression  | simpleNN |
| ------------- | -------------    | ------------- | -------------  | ------------- |
| mean RSME |64.5           | 68.15            | 3.60e+12      | 88.7 |


### **3. Sentiment classification in Faces**

---

In this project, there is an interest in classifing the sentiment of a face. That being said we will fine tune the ResNet-50 for the analysis of sentiments in faces. For this, I froze the first 30 layers of ResNet50 pre-trained for imagenet. With this step done, I added one more layer of 64 neurons and another for classification. This penultimate layer needs to have the same number of neurons as the text model so that the multimodal model gives the same relevance to the two unimodal models.

In order to train the model, I use the FER-2013 in conjunction with some data collaboratively made available via a git repository. This is done so that I have a better balanced dataset. However, as the data is annotated on emotions such as anger, happy, disgust, fear, sadness, and surprise, so I map the emotions anger and sadness as negative feelings, happiness as positive, and neutral as neutral. While the other feelings are discarded because, in my opinion, they are not well defined.

### **4. Sentiment classification in Text**

---

For the spech polarity classification I use a transformer based model named BERT. This model is characterized for being a bidirectional model composed by transformers. BERT's bidirectional representation is able to extract context in both directions of the sentence. 

The classification of sentiment polarity in text is characterized as a semantic problem. As a result, I used BERT's "CLS" token, followed by dense layers connected with skip-connection, and finally a last layer of softmax activation for classification, that activation generates a probability distribution, thus generating the probability of the classes.

Skip-connections were added to the model in order to reduce vanishing/exploding gradient problems. The idea of ​​these connections is to allow the model to learn a delta variation without the model losing the signal of the previous layers.

Regarding the training of the text model, I used the IMDB dataset, this database presents 50000 movie reviews classified as positive and negative. Thus, for the fine tuning of the classical architecture, I use BERT as an encoder for the generation of embeddings, thus training as if I were freezing the BERT layers and training only the dense network with skip connections.


### **5. Multimodel model**

---

From the trained image and text models, we extract the output of the layers prior to the classification layers. We chose to extract the output from the penultimate layer, as it carries significant value for classification. This is done so that we have a vector of characteristics that represents each modality, which can be used as input to the multimodal model. In this way, the hybrid multimodal model will be defined as a simpler network, which will unite the two feature vectors generated by the unimodalities through a attention fusion layer.


That said, each embedding goes through a dense layer, then a merge block is applied followed by a dropout and a classification layer. For this project I chose attention to the modelities as the fusion layer.

---

## Notebooks

Please see the following tutorial notebooks for a guide on how to use this project
1. **Image Feature Extraction**
    - **Image Feature Extraction** : Extracting features from the images using SIFT descriptors and training the kmeans model in order to describe the images via a BOVW | [notebook](https://colab.research.google.com/drive/**1J5B1rTAGaAfFelf8P9d4lXzjjH1j_WBr**#scrollTo=rLKUJZz0eCGp)
2. **Face Extraction model**
    - **Face Extraction model part 1** : Training the classic models for sentiment classification based on the BOWV extracted features | [notebook](https://colab.research.google.com/drive/14EdCEEEd_vQPIMs6hrZXmW4ZPI250XAt#scrollTo=GivtF11O1irD)
      - **Usage Example Face Extraction model part 1** : simple example of how to use some classic models with the BOVW model for face extraction | [notebook](https://colab.research.google.com/drive/1pNhG9B1uXoCzdrvPrtro_vuRJgY8rYtD?usp=sharing)
    
    - **Face Extraction model part 2** : Training the NN models for sentiment classification based on the BOWV extracted features | [notebook](https://colab.research.google.com/drive/1i5NsDxVnRSCGx2uGFjvhZbfw2RYgyGH9?usp=sharing)
      - **Usage Example Face Extraction model part 2** : simple example of how to use the NN models with the BOVW model for face extraction | [notebook](https://colab.research.google.com/drive/10T90dbLjuEftYs7sJT28SVzVT-R6Zrt7?usp=sharing)
3. **Sentiment Classification in Faces**
   - **Finetuning RESNET-50** : In this notebook I train the RESNET-50 for sentiment classification in faces for the FER-2013 dataset | [notebook](https://drive.google.com/file/d/1ikRLxBHUBzAoIGYc9EMNFGWmpcnQouYB/view?usp=sharing) 
4. **Sentiment Classification in Text**
   - **Finetuning DistilBERt**: In this notebook I use the distilBERT model in order to extract embeddings from the text, then I create a simple NN for classification, to train this simple NN I used the IMDB dataset | [notebook](https://drive.google.com/file/d/1rx0_qJihVTI8dUdJY28gv336bpnmClJl/view?usp=sharing)
5. **Sentiment Classificaiton in Videos using multimodel model**
   - **Final Aplication** - This aplication gets the **ID** of a youtube video, downloads it, then separetes in each channel. the text channel is classified by the text model, the video channel is classified via face extraction followed by the face classification model. Then the multimodel model is trained and the final result is generated | [notebook](https://drive.google.com/file/d/1dgfomn0gcm3Hd96Mm5r2BR8gDaEF2EPi/view?usp=sharing) 
---
## **Results**
### **1. Face Extraction models**

When a new image is added to the pipeline, we extracted the features using the SIFT+kmeans model that we trained in the *Image extraction* step, then we apply the modelos presented in *Face Extraction model part 1*. 

For the models presented in *Face Extraction model part 2* we expand the bouding box in order to fit the face a litte bit better.

By doing so we get the following results : 

NN result    | Grad result | KNN result  |DNN result   |DNN-skip result
:-----------:|:-----------:|:-----------:|:-----------:|:-----------:
| ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/results_face_extraction_p1_v1/image_NN_result.png) | ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/results_face_extraction_p1_v1/grad_boost_result.png) | ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/results_face_extraction_p1_v1/image_knn_result.png)  | ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/results_face_extraction_p2_v1/Simple%20NN.png) | ![](https://raw.githubusercontent.com/Vakihito/Face-extraction/main/images/results_face_extraction_p2_v1/Simple%20NN%20with%20skip.png) |


### **5. Sentiment analisys in videos**

Finally, for the results of each version of face extraction, I changed the aplication that extract the video from Youtube and then run each part of this project over the video. I didn't talk about the diferences of each version because it would make the workflow a little bit hard to explain. But I'll talk a little bit about it on the "Points" Section. 

This final results are only using the best model for face extraction, BOWV followed by DNN-skip. 

By doing so we get the following results for each version of face extraction: 

<div align="center">

|V1 | V2 | V3 |
|:-:|:--:|:--:|
| ![](https://github.com/Vakihito/Face-extraction/blob/main/images/final%20result/V1/V1.gif?raw=true) | ![](https://github.com/Vakihito/Face-extraction/blob/main/images/final%20result/V2/V2.gif?raw=true) | ![](https://github.com/Vakihito/Face-extraction/blob/main/images/final%20result/V3/V3.gif?raw=true)

</div>

---
## Points

The main points that i would like to talk abour is the problems related with the version 1 e 2, in V1 i tried extracting the BOWV via SIFT descriptors, and it didn't really work ... In version V2 i tried using expanding the dataset by using the Celeb Dataset by doing so it made version a little bit better, I also tried using LBP but it also didn't really work ... For version V3 I resize the image then use the points of the SIFT descriptor, followed by quantization and create a histogram from it. The ideia is that there will be more SIFT points in the face region.

All in all, I think that it works quite well, the face expression model is able to classify the face polarity quite well, the same goes to the text classification model. And even though the V3 face extraction model is not perfect the face classificaiton model still is able to classify the face polarity wich is the main gol. 

---
## Student
The work distribution was : Victor Akihito did everything !

  - Victor Akihito Kamada Tomita | 10692082 | akihito012@usp.br 

---
## References
  - [Wider Dataset](http://shuoyang1213.me/WIDERFACE/)
  - [Celeb Dataset](https://mmlab.ie.cuhk.edu.hk/projects/CelebA.html)
  - [SKLearn](https://scikit-learn.org/stable/modules/multiclass.html)
  - [FER-2013](https://arxiv.org/pdf/1910.06044.pdf)
  - [IMDB](https://www.kaggle.com/datasets/lakshmi25npathi/imdb-dataset-of-50k-movie-reviews)
