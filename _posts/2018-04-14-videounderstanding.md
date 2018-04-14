---
layout: post
title: Action Recognition 的一点理解
date: 2018-4-14
header-img: "img/1.jpg"  
categories: blog
description: Markdown tutorial
---

# Action Recognition Task

顾名思义，给定视频序列，预测动作类别，可以对应于img的分类任务。

## Existed Methods

网上有非常多的资料，我不再重复，本文仅仅讨论我个人在实验中发现的结论。
C3D 网络是一种比较基础的结构，基本靠3d conv来同时完成spatial和temporal的建模。
paper探讨了kernel size大小对于performance的影响。最终结论是3x3x3的kernel size最好。
kernel 操作本身是一个非常**local**的，通过多层堆叠网络会获得一个比较大的视野(参考receptive field概念)

## Failure Case
以下问题是目前实验中遇到的，对结论的猜测之后会一一验证。

这里提出我发现的第一个问题。采样帧率

为了图方便，我在dataloader实现时直接使用skvideo读取源视频，得到一个**CxTxWxH**大小的tensor。而skvideo貌似是保持视频原有帧率。
为了跑一个baseline，我随机初始化了C3D网络 按照官方split-1 划分数据进行训练。 在training set上很快overfitting acc达到100%，而test set上只有可怜的30%。
看到网上各种随手复现就70% acc的帖子，我心好痛。。。

为了一个好的baseline，我参照别人的方法先抽样视频，这里我选取的采样频率是 5 frame/s 之后在把一张张图片按时间维度concat起来。依旧按照官方split-1划分。
training set依旧overfitting到100%，不过test set上acc达到35%. 

这里，我没有做进一步的实验去验证采样频率是否还会带来性能上的改变。不过3x3x3的大小每次只看很小的区域，如果我们采的很密的视频，一小段范围内动作的变化很小，从temporal上看几乎是常数。
此时3x3x3的大小是不是还是最优，我没有实验过。

第二个问题是数据集划分。
待更新。
