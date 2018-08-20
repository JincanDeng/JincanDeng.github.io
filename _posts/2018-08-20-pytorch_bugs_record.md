---
layout: post
title: Some record for pytorch debugging
tags: [pytorch]
---

最近由MXNet框架转到了PyTorch框架上，原因无他，只是因为网上PyTorch开源的项目、paper代码多，且PyTorch使用一种动态图机制，非常灵活。虽然PyTorch易学易用，但笨拙的我仍然踩到了很多坑，所以想把这些坑纪录下来，供大家参考。

+ view()函数报错提示：

`RuntimeError: invalid argument 2: View size is not compatible with input tensor's size and stride (at least one dimension spans across two contiguous subspaces). Call .contiguous() before .view().`
原错误代码如下：
`targets = targets.unsqueeze(dim=1)`
`targets = targets.expand(-1, 8)`
`targets = targets.view(-1)`
错误是由于对变量targets进行了维度变换，而在进行维度变换的过程中，程序进行的是浅拷贝。正确的代码应该如下：
`targets = targets.unsqueeze(dim=1)`
`targets = targets.expand(-1, 8)`
`targets = targets.contiguous().view(-1)`
