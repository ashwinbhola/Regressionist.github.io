---
layout: post
title: Image inpainting
subtitle: ....been wanting to do this for a while
image: /img/Inpainting/paintingbot.jpg
---

This won't be a true post on Generative Adversarial Networks if it didn't start with the following quote by Yann LeCun:
>This (GANs), and the variations that are now being proposed is the most interesting idea in the last 10 years in ML, in my opinion.

Imagine that I ask you to complete a jigsaw puzzle. How do you think the human brain does it? There are two pieces of information available to your brain: contextual information and perceptual information. Contextual information is the clues about the missing parts provided by the surrounding portions of these missing regions and perceptual information helps you to infer what looks 'normal' among all the possible completions of the puzzle. Without contextual information, you don't know what to fill in and without perceptual information, any continuity inferred from the surrounding puzzle pieces is both right and wrong. The key relationship between images and statistics is that we can interpret images as samples from a high-dimensional probability distribution and we are trying to generate data points from this distribution while completing the puzzle.

I've spent the last couple of weeks (three, I think) trying to implement a DCGAN (Deep Convolutional Generative Adversarial Networks) architecture to fill in the missing portions in an image (inspired by this [paper](http://iizuka.cs.tsukuba.ac.jp/projects/completion/data/completion_sig2017.pdf)). In the most simple terms, GAN is basically a 2 player zero-sum non-cooperative game i.e. if one player wins, the other loses. Who are these players? One of them is a generator, which will be generating images and the other is a discriminator learning to differentiate the original images from the fake ones. Ultimately, we want our generator to produce images good enough to fool the discriminator. In the language of game theory, GAN model converges when the discriminator and generator reach Nash equilibrium and in the language of linear algebra, **duality**.

Since the generator and discriminator are trying to defeat each other, the objective function of GAN is a min-max function where the generator is trying to minimize what the discriminator is trying to maximize. For maximizing, gradient ascent is performed and for minimizing, gradient descent is performed on the objective function. We let the discriminator play first so that the generator learns from the discriminator's mistakes i.e. the generator will try to maximize the likelihood of the discriminator being wrong. 

Talking about image inpainting, I used the CelebA dataset, which has about 200,000 images of celebrities. I generate a mask of the same size as input image which takes the value 1 inside the regions to be filled in and 0 elsewhere. The holes in the images are replaced by the mean pixel value of the entire training set. There are three phases of training. In the first phase, the corrupted image is fed to the generator which tries to minimize the mean square error between its output and the original image. In the second phase, the discriminator is trained to distinguish the fake images from the real ones using Binary Cross Entropy Loss. After pretraining both these networks individually, these are trained together in the final phase where the generator is trying the minimize the MSE error between its output and the original image and maximize the likelihood of the discriminator being wrong whereas the discriminator is again trained with the objective of discerning whether the image is real or fake.

The discriminator network is basically a stack of two networks, a global context discriminator and a local context discriminator. As the names suggest, both the networks get different inputs from the same image. The global discriminator looks at the entire image to assess if it is coherent as a whole, while the local discriminator looks only at a small area centered at the completed region to ensure the local consistency of the generated patches..This is similar to the wisdom of crowds I talked about in my earlier blog post. The completion network is composed of 17 convolution layers each of them being followed by batch normalization and a ReLU unit, except the last one, which is followed by a sigmoid unit. The advantages of batch normalization are that it reduces the covariance shift, allows each layer of a network to learn by itself a little bit more independently of other layers and has slight regularization effects. The optimizer used is Adadelta, which is an extension of Adagrad that seeks to reduce its aggressive, monotonically decreasing learning rate. Instead of accumulating all past squared gradients, Adadelta restricts the window of accumulated past gradients to some fixed size.

Also, in the generator network, dilated convolutions are used at lower resolutions. This is important for the image completion task as the model can effectively “see” a larger area of the input image when computing each output pixel than with standard convolutional layers (contextual information). Dilated convolutions use kernels that are spread out, allowing to compute each output pixel with a much larger input area, while still using the same amount of parameters and computational power. 

## Enough theory. Show me the results!
Masked images:

![alt text](/img/Inpainting/masked.png)

Generated images:

![alt text](/img/Inpainting/pic.png)

Some learnings from this project:
1. GANs need a lot of time to train and one should not think that the model is not learning just because the loss is not decreasing for the first few mini-batches. Don't kill the training early!
2. Often, we interpret our evaluation metric as the measure of the how well our model is performing. This is not the case with GANs. MSE on the validation set was increasing when I was training but the image quality was also getting better. Thus, the only way to measure if your model is learning is to verify the images manually.
3. One way in which the game will collapse is if the loss of discriminator goes to zero. Thus, you should stop the training and change your parameters if the loss of the discriminator approaches zero. 
4. For the generator, you should use larger kernels at the top convolutional layers to maintain smoothness in the generated portions. 
5. Batch normalization definitely helps the final result. But, if you have incorrectly set your kernels or filters, or if the discriminator loss quickly reaches 0, adding batch norm might not really help recover from that.

Code: [GitHub](https://github.com/Regressionist/Image-Inpaiting-DCGAN)



Thanks,<br/>
Ashwin

