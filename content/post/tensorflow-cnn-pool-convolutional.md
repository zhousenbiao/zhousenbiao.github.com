+++
categories = ["深度学习"]
date = "2018-01-16T21:43:25+08:00"
draft = false
slug = "tensorflow-cnn-pool-convolutional"
tags = ["tensorflow","CNN"]
title = "利用Tensorflow和matplotlib直观理解CNN的卷积层与池化层"
gitment = true

+++
卷积神经网络，CNN（Convolutional Neural Network），卷积神经网络是一种多层神经网络，擅长处理图像相关的深度学习问题。 与普通神经网络的区别在于，卷积神经网络包含了由卷积层（Convolutional layer）和池化层（Pooling layer）构成的特征抽取器。 本文旨在通过调用tensorflow封装好的卷积操作和池化操作函数以及python的matplotlib库，直观地感受一张图片经过卷积层和池化层之后的效果，代码如下：

#!/usr/bin/python
# -*- coding: UTF-8 -*-
import matplotlib.pyplot as plt
import tensorflow as tf
from PIL import Image
import numpy

img = Image.open('dog.jpg')
img_ndarray = numpy.asarray(img, dtype='float32')
print(img_ndarray.shape)
img_ndarray=img_ndarray[:,:,0]
plt.figure()
plt.subplot(221)
plt.imshow(img_ndarray)

w=[[-1.0,-1.0,-1.0],
   [-1.0,9.0,-1.0],
   [-1.0,-1.0,-1.0]]

with tf.Session() as sess:
    img_ndarray=tf.reshape(img_ndarray,[1, 633, 650,1])
    w=tf.reshape(w,[3,3,1,1])
    img_cov=tf.nn.conv2d(img_ndarray, w, strides=[1, 1, 1, 1], padding='SAME')
    image_data=sess.run(img_cov)
    print(image_data.shape)
    plt.subplot(222)
    plt.imshow(image_data[0,:,:,0])

    img_pool=tf.nn.max_pool(img_ndarray, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1],
                   padding='SAME')
    image_data = sess.run(img_pool)
    plt.subplot(223)
    plt.imshow(image_data[0, :, :, 0])
    plt.subplot(224)
    img_pool = tf.nn.max_pool(img_ndarray, ksize=[1, 4, 4, 1], strides=[1, 4, 4, 1],
                              padding='SAME')
    image_data = sess.run(img_pool)
    plt.imshow(image_data[0, :, :, 0])
    plt.show()

