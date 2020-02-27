---
title: Roi pooling & Roi align
key: 20200226
tags: 深度学习 目标检测

---

- [Roi pooling](#Roipooling)
- [Roi align](#Roialign)
- [Code](#code)

Roi pooling
---
1. 起源于Faster RCNN中的RPN网络

对于原始图像上的ROI（Region of Interest）区域，使用RPN网络将ROI映射到当前特征图上。其中ROI pooling的作用就是把大小形状各不相同的ROI区域归一化为固定尺寸的feature map用于目标识别。

2. 具体细节
**特征映射+maxpooling**
![pooling](https://s2.ax1x.com/2020/02/26/3Nt6aj.png)

1) 输入大小为800\*800的原图，经过backbone网络提取的特征图大小为25\*25。
2) 假定原图中的候选区域，即ROI大小为665\*665，这样映射到特征图上的区域大小为：**665\*(25/800)**，即20.78\*20.78。**不保留小数，进行取整量化。** 得到的特征图大小为20\*20。
3) 假定$(pool_w,pool_h) = (7,7)$, 即最终的特征图大小为7\*7。这时将20\*20的特征图分成49个同等大小的区域，每个区域的大小为20/7=2.86，即2.86\*2.86，**不保留小数，进行取整量化。** 区域大小变为2\*2。
4) 在每个2\*2的区域中做maxpooling操作，取出最大的像素值作为该区域的值。组成7\*7大小的特征图。

经过两次浮点数取整操作，原本在特征图上映射的20\*20的区域，偏差成14\*14的，这样的像素偏差对后续的回归定位带来了误差，因此Roi align的提出就是为了解决这个偏差问题。

Roi align
---
1. 具体细节
**特征映射+双线性差值**
![align](https://s2.ax1x.com/2020/02/26/3NUxVe.png)
1）与roi pooling相同，得到25\*25的特征图，特征映射中**保留浮点数**，即映射后的特征图大小为20.78\*20.78。划分的区域大小为2.97\*2.97。
2）假定采样数为4，表示对每个2.97\*2.97的区域平分4份，每一份取其中心点位置，而**中心点的像素值采用双线性插值算法**进行计算，最后取四个像素值的最大值作为这个区域的值。组成7\*7大小的特征图。

2. 双线性差值
在两个方向进行一次线性插值。
![1](https://s2.ax1x.com/2020/02/26/3NdaTg.png)
假定我们向得到图中P点的值，已知$Q_{11}, Q_{12}, Q_{21}, Q_{22}$四个点的值，首先在x方向进行线性插值，得到：
![2](https://s2.ax1x.com/2020/02/26/3NdU0S.png)
然后在y方向进行线性插值，得到：
![3](https://s2.ax1x.com/2020/02/26/3NdNm8.png)
综合起来就是双线性插值最后的结果：
![4](https://s2.ax1x.com/2020/02/26/3NdJ6P.png)
相当于(x,y)的像素值为周围4个点的像素值的权值和。

Code
---
```
feature_map = input[0]
roi = input[1]

# re-project
x, y, w, h = roi[0,0], roi[0,1], roi[0,2], roi[0,3]
original_img_size = self.input_size[0]
feature_map_size = feature_map.shape[1].value
spatial_scale = original_img_size / feature_map_size
x = K.cast(x / spatial_scale, 'int32')
y = K.cast(y / spatial_scale, 'int32')
w = K.cast(w / spatial_scale, 'int32')
h = K.cast(h / spatial_scale, 'int32')
# avoid out of bounds
x_ = x + K.maximum(w, 1)
y_ = y + K.maximum(h, 1)
feature_map = feature_map[:, y:y_, x:x_, :]
        
# Roi pooling
pooled_features = MaxPooling2D(pool_size=(self.pool_size, self.pool_size), padding='SAME')(feature_map)

# Roi align
aligned_features = tf.image.resize_images(feature_map, (self.pool_size, self.pool_size))
```

---
参考文献：
[RoIPooling与RoIAlign的区别](https://blog.csdn.net/kk123k/article/details/86563425)

[图像处理之双线性插值法](https://blog.csdn.net/qq_37577735/article/details/80041586)
