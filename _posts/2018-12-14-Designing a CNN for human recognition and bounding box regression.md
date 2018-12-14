---
layout: post
title: Designing a CNN for human recognition and bounding box regression
subtitle: My learnings from this project
image: /img/hello_world.jpeg
---

To be a data scientist, you need to know how to be a scientist first.
I didn't believe in this statement before I started working on this project. I thought of data science as being closer to engineering than to science but I stand corrected. Even after Scikit-learn and other tools that have aided data scientists in applying the machine learing models to the data, the main job of a data scientist which is data collection and preparation (called janitor work by ____) can't be accomplished using online libraries or packages. It's the only part of the whole data science pipeline that has not been automated yet (although there are certain rules you can follow in addition to applying your creativity).


CNN Ninjas may say what I did can be improved by transfer learning or other deep learning methodologies but my objective was to build a CNN which learns employee classification and bounding box regression from the limited data it is given while mantainning a high accuracy in real time. I used ChokePoint Dataset which consists of photos of 30 employees clicked using 4 different security cameras. The coordinates of their eyes are given along with employee ID. There are also photos where there is no human in the frame. Also, I am in no way saying my CNN is the over and above all the face detections models out there.


In order to train a convolutional neural network, we need a relatively large dataset. The word large here has 2 meanings: it should have enough pics of all the subjects and also there should be enough pics to prevent potential overfit. I started building my CNN based on porject report of some stanford students who worked on the same dataset with the same objective. I implemented it using PyTorch and was able to replicate their results in terms of similar scores on the evaluation metrics (IoU and classification accuracy). IoU or Intersection over Union is generally the metric used to evaluate your model's output bounding box to the ground truth. IoU above 0.5 is considered satisfactory and the above mentioned model had an IoU of 0.58.


With this in mind, first I augmented my training dataset by flipping all the images in the training dataset horizontally (thanks to Marina) which doubled the images in my dataset. Then I added Gaussian noise (thanks to Serkan) to these images, further doubling the number of images in my training daaset. This was the logical thing to do because the predictions of bounding box were very poor when the person was on the left side in the frame or the image was blurred. After this, the IoU imporved to 0.61 but I am not here to celebrate mediocrity, right?  


I thought of now changing the model and came up with the idea of stacking two CNNs. I designed two six layered CNNs and avergaged their resulting predictions. The two CNNs differed only in their filter sze. It's very similar to the philosophy of wisdom of crowds but my individuals are not expert themselves and jusy two individuals don't form a crowd. Still, I trained the model on the same training dataset and got 0.71 AUC and 94% classification accuracy. The predictions of the bounding box are shown below and I think they are pretty good. 


This is still a project in progress and thus, please let me know any suggestions you have (and if you think I am incapable of thinking them myself). The code is on GitHub but I have not added the comments yet, so please hold off your judgements about my commenting skills :)


