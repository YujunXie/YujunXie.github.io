---
title: Tensowflow实践(1)：环境配置与入门
key: 20171208
tags: 深度学习 Tensorflow
---

> TensorFlow™ 是一个采用数据流图（data flow graphs），用于数值计算的开源软件库。节点（Nodes）在图中表示数学操作，图中的线（edges）则表示在节点间相互联系的多维数据数组，即张量（tensor）。它灵活的架构让你可以在多种平台上展开计算，例如台式计算机中的一个或多个CPU（或GPU），服务器，移动设备等等。TensorFlow 最初由Google大脑小组（隶属于Google机器智能研究机构）的研究员和工程师们开发出来，用于机器学习和深度神经网络方面的研究，但这个系统的通用性使其也可广泛用于其他计算领域。

什么是**数据流图**（Data Flow Graph）?

> 数据流图用“结点”（nodes）和“线”(edges)的有向图来描述数学计算。“节点” 一般用来表示施加的数学操作，但也可以表示数据输入（feed in）的起点/输出（push out）的终点，或者是读取/写入持久变量（persistent variable）的终点。“线”表示“节点”之间的输入/输出关系。这些数据“线”可以输运“size可动态调整”的多维数据数组，即“张量”（tensor）。张量从图中流过的直观图像是这个工具取名为“Tensorflow”的原因。一旦输入端的所有张量准备好，节点将被分配到各种计算设备完成异步并行地执行运算。

![tensors_flowing.gif](https://i.loli.net/2018/08/22/5b7cf0758621f.gif)

**特征**：

 - 高度的灵活性
 - 真正的可移植性
 - 将科研和产品联系在一起
 - 自动求微分
 - 多语言支持
 - 性能最优化

TensorFlow是Google开发的一个用于机器学习的开源工具库。核心代码用C++实现，可以用CPU或GPU跑。类似的 *深度学习框架* 还有Caffe, Torch, PyTorch, Keras等。

深度学习框架包含的核心组件：

1. 张量（Tensor）
2. 基于张量的各种操作
3. 计算图（Computation Graph）
4. 自动微分（Automatic Differentiation）工具
5. BLAS、cuBLAS、cuDNN等拓展包（使用底层计算软件加速运算）

<!--more-->

**安装**

TensorFlow支持windows, macOS，Linux.
[下载及安装](http://tensorfly.cn/tfdoc/get_started/os_setup.html)

目前由于实验室机器还没配好编译环境，所以只在windows和macOS上安装成功，用CPU跑。

由于担心环境依赖混杂，我使用了基于virtualenv的安装。安装成功后写个小程序测试下。
我的已经可以运行了，但是出现这样一个提示：![屏幕快照 2017-11-13 20.30.04.png](https://i.loli.net/2018/08/22/5b7cf075333c0.png)

应该是计算能力的原因，简单的程序还是跑的没问题的。

virtualenv是一个创建隔绝的Python环境的工具。virtualenv创建一个包含所有必要的可执行文件的文件夹，用来使用Python工程所需的包。
删除这个文件夹就相当于删除了一个虚拟环境。(注意记住创建的虚拟环境的位置，可以使用virtualenvwrapper来管理虚拟环境）

----------

**基础**

 - 使用图 (graph) 来表示计算任务.
 - 在被称之为 会话 (Session) 的上下文 (context) 中执行图.
 - 使用 tensor 表示数据.
 - 通过 变量 (Variable) 维护状态.
 - 使用 feed 和 fetch 可以为任意的操作(arbitrary operation) 赋值或者从其中获取数据.

TensorFlow中最基本的单位是常量（Constant）、变量（Variable）和占位符（Placeholder）。常量定义后值和维度不可变，变量定义后值可变而维度不可变。在神经网络中，变量一般可作为储存权重和其他信息的矩阵，而常量可作为储存超参数或其他结构信息的变量。占位符并没有初始值，它只会分配必要的内存。


----------

TensorFlow模型构建流程

![ten.jpg](https://i.loli.net/2018/08/22/5b7cf075270d0.jpg)

第一步：构建计算图

构建整个模型的架构。如在神经网络模型中，我们需要从输入层开始构建整个神经网络的架构，包括隐藏层的数量、每一层神经元的数量、层级之间连接的情况与权重、整个网络每个神经元使用的激活函数等内容。此外，我们还需要配置整个训练、验证与测试的过程。例如在神经网络中，定义整个正向传播的过程与参数并设定学习率、正则化率和批量大小等各类训练超参数。


第二步：将训练数据或测试数据等馈送到模型中

需要打开一个会话（Session）来执行参数初始化和馈送数据等任务。例如在计算机视觉中，我们需要随机初始化整个模型参数数值，并将图像成批（图像数等于批量大小）地馈送到定义好的卷积神经网络中。

第三步：更新权重并获取返回值

控制训练过程与获得最终的预测结果。

----------

    import tensorflow as tf
    #常量
    a = tf.constant(2)
    b = tf.constant(3)
    #变量
    w = tf.Variable(tf.random_normal([2,3],stddev=1,seed=1))
    #占位符
    x = tf.placeholder(tf.int16)
    y = tf.placeholder(tf.int16)
    add = tf.add(x,y)
    mul = tf.mutiply(x,y)
    #计算图
    with tf.Session() as sess:
        print("constant a: %i" % sess.run(a), "constant b: %i" % sess.run(b))
        #并行初始化所有变量
        sess.run(tf.initialize_all_variables())
        print("variable w: %i" % sess.run(w))

        print "Addition: %i" % sess.run(add, feed_dict={a: 2, b: 3})
        print "Multiplication: %i" % sess.run(mul, feed_dict={a: 2, b: 3})

----------


**具体学习参考**：

[TensorFlow中文社区](http://www.tensorfly.cn/)
[香港科技大学TensorFlow课件分享](https://zhuanlan.zhihu.com/p/29936078)
