+++
categories = ["深度学习"]
date = "2018-08-02T15:04:05+08:00"
draft = false
gitment = true
slug = "tensorflow-version-error"
tags = ["tensorflow"]
title = "tensorflow版本问题，导致 'DecodeJpeg/contents', does not exist in the graph."

+++

从tensorflow的github克隆下来后，git checkout r1.6
然后在tensorflow目录下跑flower_photos数据集

训练命令如下：
```
python examples/image_retraining/retrain.py --learning_rate=0.0001 --testing_percentage=20 --validation_percentage=20 --train_batch_size=32 --validation_batch_size=-1  --flip_left_right True --random_scale=30 --random_brightness=30 --eval_step_interval=100 --how_many_training_steps=600 --architecture mobilenet_1.0_224 --image_dir ~/Documents/yolo-darkne/flower_photos
```
训练结束后，在/tmp/imagenet目录下得到模型文件和对应标签文件：output_graph.pb、output_labels.txt

用如下代码调用模型：
``` python
# -*- coding:utf-8 -*-
import tensorflow as tf
import sys
 
# 命令行参数，传入要判断的图片路径
image_file = sys.argv[1]
#print(image_file)
 
# 读取图像
image = tf.gfile.FastGFile(image_file, 'rb').read()
 
# 加载图像分类标签
labels = []
for label in tf.gfile.GFile("/tmp/imagenet/output_labels.txt"):
    labels.append(label.rstrip())
 
# 加载Graph
with tf.gfile.FastGFile("/tmp/imagenet/output_graph.pb", 'rb') as f:
    graph_def = tf.GraphDef()
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')
 
with tf.Session() as sess:
    softmax_tensor = sess.graph.get_tensor_by_name('final_result:0')
    predict = sess.run(softmax_tensor, {'DecodeJpeg/contents:0': image})
 
    # 根据分类概率进行排序
    top = predict[0].argsort()[-len(predict[0]):][::-1]
    for index in top:
        human_string = labels[index]
        score = predict[0][index]
        print(human_string, score)
```

报错如下：
```
TypeError: Cannot interpret feed_dict key as Tensor: The name 'DecodeJpeg/contents:0' refers to a Tensor which does not exist. The operation, 'DecodeJpeg/contents', does not exist in the graph.
```
经查证，tensorflow1.2版本之后的版本，examples/image_retraining/retrain.py里已经没有DecodeJpeg/contents 这个tensor名称。
Google has updated their retrain.py in image_retraining. They removed the op:DecodeJpeg, so you can't get it.

解决办法：
在tensorflow根目录，执行git checkout r1.2将版本切到1.2，重新训练，得到模型再调用，问题解决。
