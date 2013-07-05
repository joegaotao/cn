---
date: '2013-07-04 17:34:29'
layout: page
slug: material
status: publish
title: 统计学习的心得总结
wordpress_id: '130'
group: navigation
---

### 1. 距离度量学习（Distance metric learning）
距离测度应用已经十分广泛，在人脸识别、物体识别、音乐的相似性、人体姿势估计、信息检索、语音识别、手写体识别等领域都有较好的应用。因为距离测度学习的目的即为了衡量样本之间的相近程度，而这也正是模式识别的核心问题之一。大量的机器学习方法，比如K近邻、支持向量机、径向基函数网络等分类方法以及K-means聚类方法，还有一些基于图的方法，其性能好坏都主要有样本之间的相似度量方法的选择决定，因此针对不同的问题，学习适合样本的距离测度具有很重要的意义。

阅读相关文献后，我着重对有监督距离度量学习的一些文献进行了总结。

* 2013年2月版本（欢迎讨论和补充）：[距离度量(测度)学习](https://www.dropbox.com/s/4tpkrkonxell0kb/dml.pdf)
* R包开发：<https://github.com/road2stat/sdml>
* Matlab工具箱：[**DistLearnKit**](http://www.cs.cmu.edu/~liuy/distlearn.htm)
* 相关论文资料：[近两年顶级会议上关于Distance Metric Learning的paper清单](http://blog.csdn.net/lzt1983/article/details/7831524)

### 2. 高维问题的思考
近10年来，不管是计算机界还是统计界，最火的学术问题即高维学习。不管是降维、变量选择、稀疏性问题、信号恢复，要面对的问题是类似的。如何在越来越丰富的数据中，用有限的资源做最有效的挖掘和推断呢？对于近十几年的统计来说，高维变量选择是非常重要的一部分，既要重视模型的解释性，也要注重模型的预测精度。以下是我在学习高维问题中的一些思考总结。

* 高维变量选择问题总结（还在继续完善中，欢迎补充）：[高维变量选择的思考](https://github.com/joegaotao/highDim/blob/master/highDim.pdf)

