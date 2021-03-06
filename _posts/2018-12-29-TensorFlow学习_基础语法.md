---
layout: post
title: TensorFlow学习 基础语法
date: 2018-12-29
tag: TensorFlow学习
---

参考博客：

[比官方更简洁的Tensorflow入门教程](https://blog.csdn.net/hustqb/article/details/80222055)

[TensorFlow 完整的TensorFlow入门教程](https://blog.csdn.net/lengguoxing/article/details/78456279)

-------------------
### 基础概念

1.Tensorflow是基于图的并行计算模型，包括输入层，隐藏层（一层或多层），输出层。

<img style='float:center' width="250" height="500" src="https://github.com/HEULQ/HEULQ.github.io/blob/master/images/posts/TensorFlow%E5%85%A5%E9%97%A8/TensorFlow_data_flow_graph.gif?raw=true" />

2.tensor：TensorFlow的数据中央控制单元是tensor(张量)，一个tensor由一系列的原始值组成，这些值被形成一个任意维数的数组，一个tensor的列就是它的维度。

3.node：图中的节点（op）。

4.tensor与node的关系：tensor的维度是100×5，表示共有100个样本，每个样本有5个特征，则输入层必须有5个node接收这些特征。

5.session：运行整个图需要创建session（会话）。

6.整体流程：原始数据tensor流入图中，流经各节点，在各层计算[梯度](https://www.jianshu.com/p/c7e642877b0e)，从而对所求参数进行优化。

### 准备
``` python
import tensorflow as tf
# 若编译时出现CPU错误，可添加以下代码
import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
```

### 定义数据
* **定义常量**

``` python
# 创建常量
const = tf.constant(2.0, name='const')
```

* **定义变量**

```python
# 创建变量
a = tf.Variable(2.0, name='a')
b = tf.Variable(1.0, dtype=tf.float32, name='b')
```
### 定义计算

```python
# 创建op
c = tf.add(a, b, name='c')
d = tf.add(a, const, name='d')
e = tf.multiply(c, d, name='e')
# 创建初始化op
init = tf.global_variables_initializer()
```

### 创建会话

```python
with tf.Session() as sess:
    # 首先初始化变量
    sess.run(init)
    e_out = sess.run(e)
    # 同时run多个op
    d_e_out = sess.run([d, e])
    # 两种输出方式
    print('e is', e_out)
    print('e is {}'.format(e_out))
```

输出结果如下：

```python
e is 12.0
```

### placeholder（占位符）
placeholder用来接收外部的输入，其本质是变量，其中参数值一般表示为\[None, 1\]，None表示第一维（一般表示样本数量）不确定大小，1表示第二维，特征数量为1。

```python
# 将b赋值为占位符（注意该语句要放在创建c,d,e语句之前）
b = tf.placeholder(tf.float32, [None, 1], name='b')
```

创建会话，其中sess.run(e)语句有改动，新增了一个feed_dict，将数据feed入图中，注意feed_dict为字典。另外，产生数据时用到了一个库numpy，具体是用[np.arange](https://blog.csdn.net/u011649885/article/details/76851291)，[np.newaxis](https://blog.csdn.net/molu_chase/article/details/78619731)来实现。

```python
import numpy as np
with tf.Session() as sess:
    # 首先初始化变量
    sess.run(init)
    # 在运行过程中feed占位符b的值
    e_out = sess.run(e, feed_dict={b: np.arange(0, 10)[:, np.newaxis]})
    print('e is', e_out)
```

输出结果如下：

```python
e is [[ 8.]
 [12.]
 [16.]
 [20.]
 [24.]
 [28.]
 [32.]
 [36.]
 [40.]
 [44.]]
```
