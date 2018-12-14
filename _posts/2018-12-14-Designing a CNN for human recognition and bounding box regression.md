---
layout: post
title: Designing a CNN for human recognition and bounding box regression
subtitle: My learnings from this project
image: /img/hello_world.jpeg
---

>To be a data scientist, you need to know how to be a scientist first.


I didn't believe in this statement before I started working on this project. I thought of data science as being closer to engineering than to science but I stand corrected. Even after Scikit-learn and other tools that have aided data scientists in applying the machine learning models to the data, the main job of a data scientist which is data collection and preparation (called janitor work in this [article](https://www.nytimes.com/2014/08/18/technology/for-big-data-scientists-hurdle-to-insights-is-janitor-work.html)) can't be accomplished using online libraries or packages. It's the only part of the whole data science pipeline that has not been automated yet (although there are certain rules you can follow in addition to applying your creativity).


After reading the post, CNN Ninjas may say what I did can be improved by transfer learning or other deep learning methodologies but my objective was to build a CNN which learns employee classification and bounding box regression from the limited data it is given while maintaining a high accuracy in real time. I used ChokePoint Dataset which consists of photos of 30 employees clicked using 4 different security cameras. The coordinates of their eyes were given (used for bounding box regression) along with their employee ID. There are also photos which do not have any human in the frame. Also, I am in no way saying my CNN is over and above all the face detections models out there.


In order to train a convolutional neural network, we need a relatively large dataset. The word large here has 2 meanings: it should have enough pics of all the subjects and also there should be enough pics to prevent potential overfit. I started building my CNN based on project [report](http://cs231n.stanford.edu/reports/2017/pdfs/222.pdf) of some stanford students  who worked on the same dataset with the same objective. I implemented it using PyTorch and was able to replicate their results in terms of similar scores on the evaluation metrics (IoU and classification accuracy). IoU or Intersection over Union is generally the metric used to evaluate your model's output bounding box to the ground truth. IoU above 0.5 is considered satisfactory and the above mentioned model had an IoU of 0.58.


The predictions of bounding box were quite poor when the person was on the left side in the frame or the image was blurred. To overcome this, I augmented my training dataset by flipping all the images in the training dataset horizontally (credits to [Marina](https://www.facebook.com/mmarinated)) which doubled the images in my dataset. Then I added Gaussian noise (thanks to [Serkan](https://www.facebook.com/karakulakserkan)) to these images, further doubling the number of images in my training dataset.  After this, the IoU improved to 0.62 but I am not here to celebrate mediocrity, right?  


I thought of changing the model itself and came up with the idea of stacking two CNNs. I designed two six layered CNNs and avergaged their resulting predictions. The two CNNs differed only in their filter size. It's very similar to the philosophy of **wisdom of crowds** but my individuals are not expert themselves and just two individuals don't make up a 'crowd'. Still, I trained the model on the same training dataset and got 0.71 AUC and 94% classification accuracy. The predictions of the bounding box are shown below and I think they are pretty good. 


![alt text](/img/FDR/pic1.png)
![alt text](/img/FDR/pic2.png)
![alt text](/img/FDR/pic3.png)
![alt text](/img/FDR/pic4.png)


This is still a project in progress and thus, please let me know any suggestions you have (and if you think I am incapable of thinking them myself). The code is on GitHub but I have not added the comments yet.  

