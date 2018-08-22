---
title: Tensowflow实践(3)：搭建简单神经网络
key: 20171215
tags: 深度学习 Tensorflow
---

暂时由于电脑计算能力的原因，跑不了如AlexNet,GoogleNet,ResNet等结构复杂的网络。
只要理解了简单神经网络的工作原理，复杂的网络不过是多加了些网络层提取和优化。


    '''
    简单的三层全连接网络
    '''

    import tensorflow as tf
    from tensorflow.examples.tutorials.mnist import input_data

    mnist = input_data.read_data_sets("../data/MNIST/", one_hot=True)

    #定义输入层，隐藏层，输出层节点个数
    INPUT_NODE = 784
    OUTPUT_NODE = 10
    LAYER1_NODE = 500
    #参数
    BATCH_SIZE = 100
    TRAINING_STEPS = 10000
    DISPLAY_STEPS = 1000
    LEARNING_RATE = 0.8
    REGULARAZTION_RATE = 0.0001

    #建模
    def inference(input_tensor, weights1, bias1, weights2, bias2):
        layer1 = tf.nn.relu(tf.matmul(input_tensor, weights1) + bias1)
        out_layer = tf.matmul(layer1, weights2) + bias2
        return out_layer

    def train(mnist):
        #输入层
        x = tf.placeholder(tf.float32, [None, INPUT_NODE], name='x-input')
        y_ = tf.placeholder(tf.float32, [None, OUTPUT_NODE], name='y-input')

        #生成隐藏层的参数
        weights1 = tf.Variable(tf.truncated_normal([INPUT_NODE,LAYER1_NODE],stddev=0.1))
            bias1 = tf.Variable(tf.constant(0.1, shape=[LAYER1_NODE]))

        #生成输出层的参数
        weights2 = tf.Variable(tf.truncated_normal([LAYER1_NODE,OUTPUT_NODE], stddev=0.1))
        bias2 = tf.Variable(tf.constant(0.1, shape=[OUTPUT_NODE]))

        #计算前向传播结果
        y = inference(x, weights1, bias1, weights2, bias2)

        #计算交叉熵及其平均值
        cross_entropy = tf.nn.sparse_softmax_cross_entropy_with_logits(logits=y, labels=tf.argmax(y_,1))
        cross_entropy_mean = tf.reduce_mean(cross_entropy)

        #定义交叉熵损失函数加上正则项为模型损失函数
        regularizer = tf.contrib.layers.l2_regularizer(REGULARAZTION_RATE)
        regularization = regularizer(weights1) + regularizer(weights2)
        cost = cross_entropy_mean + regularization

        #随机梯度下降优化器优化损失函数
        optimizer = tf.train.GradientDescentOptimizer(LEARNING_RATE).minimize(cost)

        #计算准确度
        correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_, 1))
        accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))

        #初始会话并开始训练过程
        with tf.Session() as sess:
            sess.run(tf.global_variables_initializer())

            validate_feed = {x: mnist.validation.images, y_: mnist.validation.labels}
            test_feed = {x: mnist.test.images, y_: mnist.test.labels}

            for i in range(TRAINING_STEPS):
                xs,ys = mnist.train.next_batch(BATCH_SIZE)
                sess.run(optimizer,feed_dict={x:xs,y_:ys})
                if i % DISPLAY_STEPS == 0:
                    #验证集
                    validate_acc = sess.run(accuracy, feed_dict=validate_feed)
                    print("After %d training steps, validation accuracy using average model is %g"%(i, validate_acc))

            #测试集
            test_acc = sess.run(accuracy, feed_dict=test_feed)
            print(("After %d training step(s), test accuracy using average model is %g"%(TRAINING_STEPS, test_acc)))
    
    train(mnist)


![屏幕快照 2017-12-14 23.01.10.png](https://i.loli.net/2018/08/22/5b7cf338208fc.png)


----------

    '''
    简单的卷积神经网络
    四层网络：
    第一层：卷积层，该卷积层使用 ReLU 激活函数，并且在后面带有平均池化层。
    第二层：卷积层，该卷积层使用 ReLU 激活函数，并且在后面带有平均池化层。
    第三层：全连接层（使用 ReLU 激活函数）。
    第四层：输出层。
    '''

    import numpy as np
    import tensorflow as tf
    from tensorflow.examples.tutorials.mnist import input_data

    # Variables used in the constructing and running the graph
    num_steps = 500
    display_step = 100
    learning_rate = 0.001
    batch_size = 128

    num_input = 784
    num_classes = 10
    dropout = 0.75

    # 图像尺寸和深度
    IMAGE_SIZE = 28
    NUM_CHANNELS = 1
    # 第一层卷积层的尺寸和深度
    CONV1_DEEP = 32
    CONV1_SIZE = 5
    # 第二层卷积层的尺寸和深度
    CONV2_DEEP = 64
    CONV2_SIZE = 5
    # 全连接层的结点个数
    FC_SIZE = 1024

    def inference(x, dropout):

        weights = {
            # 5x5 conv, 1 input, 32 outputs
            'wc1': tf.Variable(tf.random_normal([CONV1_SIZE, CONV1_SIZE, NUM_CHANNELS, CONV1_DEEP])),
            # 5x5 conv, 32 inputs, 64 outputs
            'wc2': tf.Variable(tf.random_normal([CONV2_SIZE, CONV2_SIZE, CONV1_DEEP, CONV2_DEEP])),
            # fully connected, 7*7*64 inputs, 1024 outputs
            'wd1': tf.Variable(tf.random_normal([7 * 7 * 64, FC_SIZE])),
            # 1024 inputs, 10 outputs (class prediction)
            'out': tf.Variable(tf.random_normal([FC_SIZE, num_classes]))
        }
            biases = {
            'bc1': tf.Variable(tf.random_normal([CONV1_DEEP])),
            'bc2': tf.Variable(tf.random_normal([CONV2_DEEP])),
            'bd1': tf.Variable(tf.random_normal([FC_SIZE])),
            'out': tf.Variable(tf.random_normal([num_classes]))
        }

        # MNIST data input is a 1-D vector of 784 features (28*28 pixels)
        # Reshape to match picture format [Height x Width x Channel]
        # Tensor input become 4-D: [Batch Size, Height, Width, Channel]
        x = tf.reshape(x, shape=[-1, IMAGE_SIZE, IMAGE_SIZE, 1])

        # conv2d四个参数：
        # 输入图像，即一个四维张量 [batch size, image_width, image_height, image_depth]
        # 权重矩阵，即一个四维张量 [filter_size, filter_size, image_depth, filter_depth]
        # 每一个维度的步幅数
        # Padding (= 'SAME' / 'VALID')

        stride = 1
        kernel = 2

        layer1_conv = tf.nn.conv2d(x, weights['wc1'], [1, stride, stride, 1], padding='SAME')
        layer1_actv = tf.nn.relu(layer1_conv + biases['bc1'])
        layer1_pool = tf.nn.avg_pool(layer1_actv, [1, kernel, kernel, 1], [1, kernel, kernel, 1], padding='SAME')

        layer2_conv = tf.nn.conv2d(layer1_pool, weights['wc2'], [1, stride, stride, 1], padding='SAME')
        layer2_actv = tf.nn.relu(layer2_conv + biases['bc2'])
        layer2_pool = tf.nn.avg_pool(layer2_actv, [1, kernel, kernel, 1], [1, kernel, kernel, 1], padding='SAME')

        flat_layer = tf.reshape(layer2_pool, [-1, weights['wd1'].get_shape().as_list()[0]])
        layer3_fccd = tf.add(tf.matmul(flat_layer, weights['wd1']), biases['bd1'])
        layer3_actv = tf.nn.relu(layer3_fccd)
        layer3_drop = tf.nn.dropout(layer3_actv, dropout)

        logits = tf.add(tf.matmul(layer3_drop, weights['out']), biases['out'])

        return logits

    def train(mnist):

        # tf Graph input
        X = tf.placeholder(tf.float32, [None, num_input])
        Y = tf.placeholder(tf.float32, [None, num_classes])
        keep_prob = tf.placeholder(tf.float32)  # dropout (keep probability)

        #Define loss
        logits = inference(X, keep_prob)
        loss = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logits=logits, labels=Y))

        #Define optimizer
        optimizer = tf.train.AdamOptimizer(learning_rate=learning_rate).minimize(loss)

        # Evaluate model
        prediction = tf.nn.softmax(logits)
        correct_pred = tf.equal(tf.argmax(prediction, 1), tf.argmax(Y, 1))
        accuracy = tf.reduce_mean(tf.cast(correct_pred, tf.float32))

        with tf.Session() as sess:

            sess.run(tf.global_variables_initializer())

            for step in range(num_steps):
                batch_data, batch_labels = mnist.train.next_batch(batch_size)
                # 在每一次批量中，获取当前的训练数据，并传入feed_dict以馈送到占位符中
                feed_dict = {X: batch_data, Y: batch_labels}
                sess.run(optimizer, feed_dict={X: batch_data, Y: batch_labels, keep_prob: dropout})

                if step % display_step == 0:
                    # Calculate batch loss and accuracy
                    cost, acc = sess.run([loss, accuracy], feed_dict={X: batch_data, Y: batch_labels, keep_prob: 1.0})
                    print("Step " + str(step) + ", Minibatch Loss= " + "{:.4f}".format(cost) + ", Training Accuracy= " + "{:.3f}".format(acc))

            print("Optimization Finished!")

            # Calculate accuracy for 256 MNIST test images
            # dropout只在训练过程中使用
            print("Testing Accuracy:", \
            sess.run(accuracy, feed_dict={X: mnist.test.images[:256], Y: mnist.test.labels[:256], keep_prob: 1.0}))

    def main(argv=None):
        mnist = input_data.read_data_sets("../data/MNIST/", one_hot=True)
        train(mnist)

    if __name__ == '__main__':
        tf.app.run()

![屏幕快照 2017-12-15 10.48.43.png](https://i.loli.net/2018/08/22/5b7cf337d823a.png)

----------

因为mac上用的是CPU跑的程序，当网络在5层以上电脑就比较吃力了，发热严重。这也说明神经网络的复苏离不开计算能力的提高，当然还有模型，数据和算法。

----------
参考资料：
[玩儿懂深度学习Part 3：搭建深度神经网络](https://jizhi.im/blog/post/gpu-p3?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)

[ML-Tutorial-Experiment](https://github.com/jiqizhixin/ML-Tutorial-Experiment)

[TensorFlow-Examples](https://github.com/aymericdamien/TensorFlow-Examples)
