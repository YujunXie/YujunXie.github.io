---
title: Tensowflow实践(2)：基本模型
key: 20171214
tags: 深度学习 Tensorflow
---

用tensorflow来实现一些基础模型。

 1. Linear Regression
 2. Logistic Regression
 3. Nearest Neighbor

<!--more-->

1.Linear Regression
-----------------

线性回归

    import tensorflow as tf
    import numpy
    import matplotlib.pyplot as plt

    # parameters
    learning_rate = 0.01
    training_epochs = 1000
    display_step = 50

    # data
    X = tf.placeholder(tf.float32)
    Y = tf.placeholder(tf.float32)
    W = tf.Variable(tf.random_normal([1]), name='weight')
    b = tf.Variable(tf.random_normal([1]), name='bias')
    train_X = numpy.array([3.3, 4.4, 5.5, 6.71, 6.93, 4.168, 9.779, 6.182, 7.59, 2.167,7.042, 10.791, 5.313, 7.997, 5.654, 9.27, 3.1])
    train_Y = numpy.array([1.7, 2.76, 2.09, 3.19, 1.694, 1.573, 3.366, 2.596, 2.53, 1.221,2.827, 3.465, 1.65, 2.904, 2.42, 2.94, 1.3])
    n_samples = train_X.shape[0]

    hypothesis = tf.add(tf.multiply(X, W), b)
    cost = tf.reduce_mean(tf.square(hypothesis - Y))/(2*n_samples)
    optimizer = tf.train.GradientDescentOptimizer(learning_rate).minimize(cost)

    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())
        for epoch in range(training_epochs):
            for (x, y) in zip(train_X, train_Y):
                sess.run(optimizer, feed_dict={X: x, Y: y})

            if (epoch + 1) % display_step == 0:
                c = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
                print("Epoch: %i " % (epoch + 1), "cost=%f "% c, "W=", sess.run(W), "b=", sess.run(b))

        print("Optimization Finished.")
        training_cost = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
        print("Training cost=", training_cost, "W=", sess.run(W), "b=", sess.run(b))

        plt.plot(train_X, train_Y, 'ro', label='Original data')
        plt.plot(train_X, sess.run(W) * train_X + sess.run(b), label='Fitted line')
        plt.legend()
        plt.show()

![屏幕快照 2017-12-09 22.56.53.png](https://i.loli.net/2018/08/22/5b7cf1d066c2a.png)

![屏幕快照 2017-12-09 22.51.49.png](https://i.loli.net/2018/08/22/5b7cf1d040c93.png)

2.Logistic Regression
-----------------

逻辑回归：mnist手写数据集，softmax分类器

    import tensorflow as tf
    from tensorflow.examples.tutorials.mnist import input_data
    import matplotlib.pyplot as plt
    import random

    mnist = input_data.read_data_sets("../data/MNIST/", one_hot=True)

    #parameters
    learning_rate = 0.01
    training_epochs = 10
    batch_size = 100
    display_step = 1

    X = tf.placeholder(tf.float32, shape=[None,784])
    Y = tf.placeholder(tf.float32, shape=[None,10])

    W = tf.Variable(tf.zeros([784,10]))
    b = tf.Variable(tf.zeros([10]))

    hypothesis = tf.nn.softmax(tf.matmul(X,W) + b)
    cost = tf.reduce_mean(-tf.reduce_sum(Y*tf.log(hypothesis), reduction_indices=1))
    optimizer = tf.train.GradientDescentOptimizer(learning_rate).minimize(cost)

    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())
        total_batch = int(mnist.train.num_examples / batch_size)
        for epoch in range(training_epochs):
            avg_cost = 0
            for i in range(total_batch):
                batch_xs, batch_ys = mnist.train.next_batch(batch_size)
                _, c = sess.run([optimizer, cost], feed_dict={X:batch_xs, Y:batch_ys})
                avg_cost += c / total_batch

            if (epoch + 1) % display_step == 0:
                print("Epoch:%i" % (epoch+1), "cost=%f" % avg_cost)

        print("Optimization Finished.")

        correct_prediction = tf.equal(tf.argmax(hypothesis,1), tf.argmax(Y,1))
        accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
        print("Accuracy:", accuracy.eval({X:mnist.test.images[:300], Y:mnist.test.labels[:300]}))
        
        r = random.randint(0, mnist.test.num_examples - 1)
        print("Lable:", sess.run(tf.argmax(mnist.test.labels[r:r+1], 1)))
        print("Prediction:", sess.run(tf.argmax(hypothesis, 1), feed_dict={X:mnist.test.images[r:r+1]}))

        plt.imshow(mnist.test.images[r:r+1].reshape(28,28), cmap='Greys', interpolation='nearest')
        plt.show()

![屏幕快照 2017-12-10 21.48.17.png](https://i.loli.net/2018/08/22/5b7cf1d00f8d5.png)

![屏幕快照 2017-12-10 21.47.49.png](https://i.loli.net/2018/08/22/5b7cf27167da9.png)

3.Nearest Neighbor
-----------------

最近邻问题：训练集中数据与测试集中数据的距离判别。

    import tensorflow as tf
    import numpy as np
    import random
    import matplotlib.pyplot as plt
    from tensorflow.examples.tutorials.mnist import input_data

    mnist = input_data.read_data_sets("../data/MNIST/", one_hot=True)

    Xtr, Ytr = mnist.train.next_batch(5000)
    Xte, Yte = mnist.test.next_batch(200)

    xtr = tf.placeholder(tf.float32, shape=[None,784])
    xte = tf.placeholder(tf.float32, shape=[784])

    distance = tf.reduce_sum(tf.abs(tf.add(xtr, tf.negative(xte))), axis=1)
    hypothesis = tf.arg_min(distance, 0)

    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())

        accuracy = 0.
        for i in range(len(Xte)):

            nn_index = sess.run(hypothesis, feed_dict={xtr: Xtr, xte:Xte[i, :]})

            print("Test:%i" % i, "hypothesis=", np.argmax(Ytr[nn_index]), "True class:", np.argmax(Yte[i]))

            if np.argmax(Ytr[nn_index]) == np.argmax(Yte[i]):
                accuracy += 1./len(Xte)

        print("Done.")
        print("Accuracy:", accuracy)

        r = random.randint(0, 199)
        print("Lable:", sess.run(tf.argmax(mnist.test.labels[r:r + 1], 1)))
        index = sess.run(hypothesis, feed_dict={xtr:Xtr, xte:Xte[r,:]})
        print("Prediction:", np.argmax(Ytr[index]))

        plt.imshow(Xte[r].reshape(28,28), cmap='Greys', interpolation='nearest')
        plt.show()

![屏幕快照 2017-12-10 22.49.28.png](https://i.loli.net/2018/08/22/5b7cf1cff1bd2.png)

![屏幕快照 2017-12-10 22.49.36.png](https://i.loli.net/2018/08/22/5b7cf1cfbe63d.png)


----------

MNIST数据集(28*28 images)

    # Import MNIST 
    from tensorflow.examples.tutorials.mnist import input_data
    #dataset directory
    mnist = input_data.read_data_sets("/tmp/data/", one_hot=True)

    # Load data
    X_train = mnist.train.images
    Y_train = mnist.train.labels
    X_test = mnist.test.images
    Y_test = mnist.test.labels

    # Get the next 64 images array and labels
    batch_X, batch_Y = mnist.train.next_batch(64)

----------

参考资料：

[TensorFlow中文社区-首页](http://tensorfly.cn/index.html)

[TensorFlow-Examples](https://github.com/aymericdamien/TensorFlow-Examples)

[邻近算法](https://baike.baidu.com/item/%E9%82%BB%E8%BF%91%E7%AE%97%E6%B3%95/1151153?fr=aladdin)
