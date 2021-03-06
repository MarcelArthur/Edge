---
title: MachineLearning Random Forest
date: 2018-10-18 21:24:09
tags: 
- 随机森林
- 机器学习
categories: 
- 算法迷思
---

随机森林的前世今生

<!-- more -->

## 1.什么是随机森林
首先，随机森林算法是一种监督式分类算法。我们从名字上也能看出来，它是用来以某种方式创建一个森林，并使其随机。森林中的树的数量直接关系到算法得到的结果：树越多，结果就越准确。但是需要注意一点是，创建森林并不等同于用信息增益或构建索引的方法来创建决策。熟悉决策树的人在首次接触随机森林时可能会有些困惑。决策树是一种决策辅助工具，它是用一种树状的图形来展示可能的结果。如果你将一个带有目标和特征的训练集输入到决策树里，它就会表示出一些规律出来。这些规律就可以用于执行预测。举个例子，假如你想预测你的女儿是否会喜欢一部动画片，你就可以收集她以前喜欢的动画片，将某些特征作为输入。然后通过决策树算法，你就可以生成相应的规律。而收集这些信息节点和形成规律的过程就是使用信息增益方法和基尼系数计算的过程。随机森林算法和决策树算法分不同之处就是，在随机森林中，找到根节点和划分特征节点的过程会以随机的方式进行。

## 2.为何用随机算法

我们为何使用随机森林算法主要有四点原因。第一点就是如开头所说，随机森林既可用于分类问题，也可用于回归问题。第二点，过拟合是个非常关键的问题，可能会让模型的结果变得糟糕。但对于随机森林来说，如果森林中的树足够多，那么分类器就不会过拟合模型。第三点是随机森林的分类器可以处理缺失值。最后一点，随机森林分类器可以用分类值建模。

## 3.随机森林算法的现实例子
我们举出一些例子，让你更好的理解随机森林。假设小明想去不同的地方度假两周，向朋友征求意见。朋友会问小明他都去过哪些地方，喜不喜欢这些地方。根据小明的回答，朋友就开始给出建议。这里，小明的朋友用的就是决策树方法。小明想问问更多朋友的意见，因为他觉得一个朋友的方法没法帮他做出准确的决定。于是其他的朋友们同样问了小明很多随机的问题，然后给出建议。最后，小明把推荐人数最多的那个地方作为度假目的地。我们详细说说这里所举的例子。其中一位朋友问了小明一些问题，并根据他的回答给出了目的地建议。单从这里看，这是典型的决策树方法。朋友根据小明的回答形成了规律，然后用规律去找到符合规律的答案。但小明的朋友们同时还以随机的方式问了他很多不同的问题，并给出了建议，也就是对度假目的地的投票。最后，得票率最高的地方就是小明选择的目的地。这里用到的就是典型的随机森林方法。

## 4.随机森林算法的工作原理
随机森林算法又两个阶段，一个阶段是创建随机森林，另一个是根据前一个阶段创建的随机森林分类器做出预测。整个过程如下所示：

1. 从全部“m”个特征中随机选择“K”个特征，其中k << m
2. 在“K”个特征中，用最佳分裂点计算节点“d”
3. 用最佳分裂将节点分裂为子节点
4. 重复前面三步的过程，直到获得“I”个数量的节点
5. 重复第1到第4步“n”次创建“n”个树，从而形成一个森林。

在第二阶段，根据上一阶段创建的随机森林分类器，我们会做出预测。过程如下：

{% asset_img random_dataset.jpg The process of randomly extracting features from random forests %}


> * 选取测试特征，用每个随机创建的决策树的规律去预测结果，并保存预测的结果（目标）。
> * 结算每个预测目标的得票数。
> * 将得票最多的预测目标作为随机森林算法的最终预测。


## 5.随机森林的应用案例

在这部分我们列举随机森林算法在4个领域的应用案例：银行、医疗、股市和电子商务。

> * 在银行领域，随机森林算法可用于发现忠诚客户，也就是说客户经常从银行借贷并且按时还款，同样也能用于发现欺诈客户，即那些没有按时还款且行为异常的人。
> * 在医疗领域，随机森林算法能够用于识别医药中的不同成分是否以正确的方式组合在以前，也可通过分析患者的病历识别疾病。
> * 在股市方面，随机森林算法可以用于识别股票的波动行为，预估损失或收益。
> * 在电子商务方面，随机森林算法可用于根据顾客的购物经历，预测他们是否喜欢系统推荐的商品。


## 6.随机森林的优点
和其它分类方法相比，随机森林算法有三个优势：

> * 在分类问题的应用方面，随机森林算法可以避免过拟合问题。
> * 对于分类任务和回归任务，可以使用同一个随机森林算法。
> * 随机森林算法能够用于从训练数据集中识别出最重要的特征，即特征工程。

### 7.随机森林的局限性

随机森林还有一些局限性：当我们需要推断超出范围的独立变量或非独立变量，随机森林做得并不好，我们最好使用如 MARS 那样的算法。随机森林算法在训练和预测时都比较慢。如果需要区分的类别十分多，随机森林的表现并不会很好。总的来说，随机森林在很多任务上一般要比提升方法的精度差，并且运行时间也更长。所以在 Kaggle 竞赛上，有很多模型都是使用的梯度提升树算法或其他优秀的提升方法。


---

本文转载自[景略集智](https://zhuanlan.zhihu.com/p/38383952)

参考资料[从决策树到随机森林：树型算法的原理与实现](https://zhuanlan.zhihu.com/p/28217071)
