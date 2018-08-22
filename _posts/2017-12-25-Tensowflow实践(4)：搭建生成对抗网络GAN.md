---
title: Tensowflow实践(4)：搭建生成对抗网络GAN
key: 20171225
tags: 深度学习 Tensorflow
---
之前我们介绍了生成对抗网络，现在看看用tensorflow简单实现。

    import numpy as np
    from matplotlib import pyplot as plt
    import tensorflow as tf
    from tensorflow.examples.tutorials.mnist import input_data

    mnist = input_data.read_data_sets("../data/MNIST/", one_hot=True)


    #tarining params
    num_steps = 20000
    batch_size = 128
    learning_rate = 0.0001

    #network params
    image_dim = 784
    gen_hidden_dim = 256
    disc_hidden_dim = 256
    noise_dim = 100

    def glorot_init(shape):
        return tf.random_normal(shape=shape, stddev=1. / tf.sqrt(shape[0] / 2.))

    weights = {
        'gen_hidden1': tf.Variable(glorot_init([noise_dim, gen_hidden_dim])),
        'gen_out': tf.Variable(glorot_init([gen_hidden_dim, image_dim])),
        'disc_hidden1': tf.Variable(glorot_init([image_dim, disc_hidden_dim])),
        'disc_out': tf.Variable(glorot_init([disc_hidden_dim, 1])),
    }
    bias = {
        'gen_hidden1': tf.Variable(tf.zeros([gen_hidden_dim])),
        'gen_out': tf.Variable(tf.zeros([image_dim])),
        'disc_hidden1': tf.Variable(tf.zeros([disc_hidden_dim])),
        'disc_out': tf.Variable(tf.zeros([1])),
    }

    #generator
    def generator(x):
        hidden_layer = tf.nn.relu(tf.add(tf.matmul(x,    weights['gen_hidden1']), bias['gen_hidden1']))
        out_layer = tf.nn.sigmoid(tf.add(tf.matmul(hidden_layer, weights['gen_out']), bias['gen_out']))
        return out_layer

    #discriminator
    def discriminator(x):
        hidden_layer = tf.nn.relu(tf.add(tf.matmul(x,    weights['disc_hidden1']), bias['disc_hidden1']))
        out_layer = tf.nn.sigmoid(tf.add(tf.matmul(hidden_layer, weights['disc_out']), bias['disc_out']))
        return out_layer

    #bulid networks
    gen_input = tf.placeholder(tf.float32, shape=[None, noise_dim],    name='input_noise')
    disc_input = tf.placeholder(tf.float32, shape=[None, image_dim], name='disc_input')

    gen_sample = generator(gen_input)

    disc_real = discriminator(disc_input)
    disc_fake = discriminator(gen_sample)

    gen_loss = -tf.reduce_mean(tf.log(disc_fake))
    disc_loss = -tf.reduce_mean(tf.log(disc_real) + tf.log(1. - disc_fake))

    optimizer_gen = tf.train.AdamOptimizer(learning_rate=learning_rate)
    optimizer_disc = tf.train.AdamOptimizer(learning_rate=learning_rate)

    gen_vars = [weights['gen_hidden1'], weights['gen_out'], bias['gen_hidden1'], bias['gen_out']]
    disc_vars = [weights['disc_hidden1'], weights['disc_out'], bias['disc_hidden1'], bias['disc_out']]

    train_gen = optimizer_gen.minimize(gen_loss, var_list=gen_vars)
    train_disc = optimizer_disc.minimize(disc_loss, var_list=disc_vars)

    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())

        for i in range(1, num_steps+1):
            batch_x, _ = mnist.train.next_batch(batch_size)
            z = np.random.uniform(-1., 1., size=[batch_size, noise_dim])

            #train
            feed_dict = {disc_input: batch_x, gen_input: z}
            _, _, gl, dl = sess.run([train_gen, train_disc, gen_loss, disc_loss], feed_dict=feed_dict)

            if i % 2000 == 0 or i == 1:
                print('Step %i: Generator Loss: %f, Discriminator Loss: %f' % (i, gl, dl))


    #test
    n = 6
    canvas = np.empty((28*n, 28*n))
    for i in range(n):
        #input noise
        z = np.random.uniform(-1., 1., size=[n, noise_dim])
        #generate image from noise
        g = sess.run(gen_sample, feed_dict={gen_input: z})
        #reverse colos for better display
         = -1 * (g - 1)
        for j in range(n):
            canvas[i * 28:(i + 1) * 28, j * 28:(j + 1) * 28] = g[j].reshape([28,28])

    plt.figure(figsize=(n,n))
    plt.imshow(canvas, origin="upper", cmap='gray')
    plt.show()

![屏幕快照 2017-12-24 18.01.48.png](https://i.loli.net/2018/08/22/5b7cf8f00d115.png)

![下载.png](https://i.loli.net/2018/08/22/5b7cf8ed76007.png)
