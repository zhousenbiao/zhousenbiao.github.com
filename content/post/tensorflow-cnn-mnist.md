+++
date = "2017-12-08T13:04:48+08:00"
draft = false
gitment = true
slug = "tensorflow-cnn-mnist"
title = "利用Tensorflow构建CNN，步骤梳理"
categories = ["深度学习"]
tags = ["Tensorflow","CNN"]
+++
利用Tensorflow构建CNN卷积神经网络，以mnist手写识别为例，步骤梳理如下：

1.定义权重函数
```
def weight_variable(shape):
    # 使用正态分布填充变量，标准差设置为0.1
    initial = tf.truncated_normal(shape, stddev=0.1)
    #返回相应的变量
    return tf.Variable(initial)
```
2.定义偏置函数
```
def bias_Variable(shape):
    # 定义一个常量该常量全部由0.1组成，shape由参数shape指定
    initial = tf.constant(0.1, shape=shape)
    # 返回一个变量，变量的值由刚刚定义的常量决定
    return tf.Variable(initial)
```
3.定义卷积层
```
def conv2d(x, W):
    return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')
```
4.定义池化层
```
def max_pool(x):
    return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')
```
5.定义CNN网络结构
```
# 构建网络，由2个卷积层（包含激活层、池化层），一个全连接层，一个dropout层，一个softmax层 组成
def deepnn(x):
    x_image = tf.reshape(x, [-1, 28, 28, 1])
    W_conv1 = weight_variable([5, 5, 1, 32])
    b_conv1 = bias_variable([32])
    h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1)+b_conv1)
    h_pool1 = max_pool(h_conv1)

    W_conv2 = weight_variable([5, 5, 32, 64])
    b_conv2 = bias_variable([64])
    h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2)+b_conv2)
    h_pool2 = max_pool(h_conv2)

    W_fc1 = weight_variable([7*7*64, 1024])
    b_fc1 = bias_variable([1024])
    # reshape成向量
  h_pool2_flat = tf.reshape(h_pool2, [-1, 7*7*64])
    h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1)+b_fc1)

    keep_prob = tf.placeholder(tf.float32)
    # dropout层
  h_fc1_drop = tf.nn.dropout(h_fc1, keep_prob)

    W_fc2 = weight_variable([1024, 10])
    b_fc2 = bias_variable([10])
    # softmax层
  y_predict = tf.nn.softmax(tf.matmul(h_fc1_drop, W_fc2)+b_fc2)

    return keep_prob, y_predict
```
6.定义损失函数、参数优化器
```
# 定义交叉熵函数，即我们使用的loss函数，我们的目的就是在训练过程中使得loss函数尽可能的小。
cross_entropy = tf.reduce_mean(-tf.reduce_sum(y_actual*tf.log(y_predict), reduction_indices=[1]))
# 使用AdamOptimizer进行loss函数的优化。使用ADAM优化算法，它对每个权值都计算自适应的学习速率，收敛速度比一般的梯度下降法更快。
train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)
```
7.定义预测函数、准确率验证
```
# 定义预测函数，这里比较预测结果和实际结果的相匹配的数量
correct_prediction = tf.equal(tf.argmax(y_predict, 1), tf.argmax(y_actual, 1))
# 精确度计算
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```
8.连接session，训练模型
```
sess = tf.InteractiveSession()
sess.run(tf.global_variables_initializer())
for i in range(20000):
    # 每次50张图片
    batch = mnist.train.next_batch(50)
    # 每训练100次，验证一次
    if i%100 ==0:
        train_acc = accuracy.eval(feed_dict={x:batch[0], y_actual: batch[1], keep_prob:1.0})
        print 'step %d, training accuracy %g' % (i, train_acc)
        train_step.run(feed_dict={x:batch[0], y_actual:batch[1], keep_prob:0.5})

test_acc = accuracy.eval(feed_dict={x: mnist.test.images, y_actual:mnist.test.labels, keep_prob:1.0})
print "test accuracy %g" % test_acc
```
由于Tensorflow的后台计算是依赖于高效的C++，因此我们需要连接到后台去运行。与后台的连接称为一个session会话。也就是说，前面搭建CNN的过程其实就是先构建一个graph图，然后在session里运行它。这就像我们家里要用自来水，就必须先接好水管，然后自来水才能在水管里运行流动。

