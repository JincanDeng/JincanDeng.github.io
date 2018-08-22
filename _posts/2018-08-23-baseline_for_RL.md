---
layout: post
title: How to choose baseline for REINFORCE algorithm?
tags: [RL]
---

REINFORCE算法是基于策略梯度的强化学习算法，目前已经应用到许多深度学习的实验中。个人认为，强化学习的引入可以解决两个问题：

+ 优化目标函数不可导
+ 全局性的序列决策问题

在REINFORCE算法中，最为关键的一步是reward函数的构建和baseline的选择。可以说，这两者的设计直接影响了REINFORCE算法是否有效。在这篇博客中，纪录了笔者所看过的论文中对于reward函数以及baseline的选择，，以供参考。


