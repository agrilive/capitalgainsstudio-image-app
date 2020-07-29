# Capital Gains Studio Image Classification Project

TechForShe, a women-in-tech empowerment community, partnered with Capital Gains Studio, a Singapore board game designer company, as part of the ImpactMakers 2019 project. 

Project period: November 2019 - February 2020

## Introduction

#### Our task

Develop a real-time image classification mobile application of game cards for educational purposes. The dataset consists of 52 cards (52 classes with one card per class).

#### Background

The game cards developed are predominantly in English. However, the company is expanding into Southeast Asia, which does not have English as its main language. It is costly to print cards of multiple languages, especially in a small market. They need a mobile app that could allow users to enjoy their games and learn despite using English cards.

#### My role

The team consists of four members and two co-leaders. My role in this project was to develop the deep learning model that can identify the image card. I started off using fast.ai pre-trained models as we only had a small dataset of 52 images. This made training a deep learning model from scratch infeasible.

However, I realised that fast.ai though intuitive for beginners, was not very supported in terms of mobile app deployment. Therefore, I looked into alternative frameworks such as Tensorflow. I realised that Keras as a high-level API for Tensorflow, is user-friendly and easy to pick up.

## Dataset creation

- [create_dataset.ipynb](https://github.com/agrilive/capitalgainsstudio-image-app/blob/master/create_dataset.ipynb) 

### Dataset Problems

#### Problem 1 - Lack of images

Our dataset consists of 52 card images - 52 classes and one image per class. 

#### Problem 2 - Training dataset is not a good representative of the test dataset (real world application)

The training dataset we have is the PNG format of the dataset (transparent and clear background). This clean dataset is not representative of the real-world noisy dataset when users take photos of the cards with their phones. Lighting/shadow, partially covered images (e.g. users whole the cards in their hands) will pose challenges to the model that is trained on a clean dataset.

### Solutions

#### Solution 1 - Use image augmentation to generate new images

Since the number of image per class is very small, we have to use image augmentations to generate new images. This is done through `ImageDataGenerator()` function. Our target is to create at least 200 new images per class using image augmentation techniques.

#### Solution 2 - Mimick the test dataset by taking photos and introducing noise

A handful of images are taken and image augmentation is used to create a larger dataset.

## Fast.ai implementation

As mentioned, fast.ai was used in the early steps of trying out different architectures and frameworks. The notebook can be found [here](https://github.com/agrilive/capitalgainsstudio-image-app/blob/master/fastai-image-classification.ipynb). Note that this was not the final model used.

## Keras implementation

- [train_model.ipynb](https://github.com/agrilive/capitalgainsstudio-image-app/blob/master/train_model.ipynb) - using a pre-trained NASNetMobile model for transfer learning

A smaller sample of 5 cards are used for demonstration here. This was part of a trial and error to find the architecture that suits our use case. 

#### Trial and error process

1. I looked up [Keras Applications documentations](http://keras.io/applications/) and tried out several architectures such as ResNet50, Xception and DenseNet121. 
    - A key consideration here is the Size of the model and its accuracy. Since we are deploying it to a mobile application, the model size needs to be small.
2. After trying out a handful of architectures, the most promising one was NASNetMobile with an intial accuracy of 94% and validation accuracy 20% using the full architecture.
    - This accuracy is much higher compared to the 20% accuracy using larger architectures like InceptionV3.
3. After settling for NASNetMobile, I tried out different sizes of the architecture by keeping the earlier cell blocks and removing the later ones, and attaching my own classification head.
    - This is done through Keras `get_layer()` function.
    - The most promising size seems to be keeping the architecture until `normal_concat_5` layer and its `activation_93` layer. 
    - I then fine-tune by keeping the layers until `normal_concat_3` frozen and the layers after it were unfreeze for fine-tuning.
    
#### Results

The model gave over 90% accuracy and validation accuracy after training for several epochs. It was successful in predicting the classes of the dataset.
