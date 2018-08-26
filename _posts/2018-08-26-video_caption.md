---
layout: post
title: Paper reading about video caption
tags: [video caption]
---

## Microsoft COCO Captions: Data Collection and Evaluation Server  
---
这篇文章详细介绍了Micorsoft收集的一个image caption的数据集，并讲解了一系列用于评价image caption质量的指标，包括BLEU、ROUGE、METEOR、CIDEr，这些评价指标同样使用于video caption的任务中。

参考这篇[博客](https://www.cnblogs.com/Determined22/p/6910277.html)

* n-gram  
  ---
  n-gram是将n个单词组成的有序序列看作一个整体，在此基础上进行评价分析的语言模型。这一模型认为，第n个单词的出现只与前n-1个单词有关（Markov Assumption），从而降低了语言模型的复杂度。

  理论上，只要有足够的语料数据，n-gram中的n越大越好。而实际中，语料数据量往往是有限的，如果n取得过大，就会导致n-gram产生的单词序列稀疏。实际使用中，n往往取1-4。

  理解n-gram时可以从n=1的情况入手，这时考虑的就是单个单词的情况（unigram）。

  符号规定：

  一张图片或一段视频或一个待翻译的句子的序数下标：i

  模型输出的预测caption：$c_i$
  
  候选的参考captions：$S_i = \{s_{i1}, s_{i2}, \cdots, s_{im}\} \in S$
  
  n-gram$\omega_k$出现在参考句子$s_{ij}$中的次数：$h_k(s_{ij})$
  
  n-gram$\omega_k$出现在模型预测句子$c_i$中的次数：$h_k(c_i)$

* BLEU(bilingual evaluation understudy)
  ---
  BLEU是机器翻译指标中的一种，衡量的是模型输出句子与参考句子之间的n-gram的包含情况。用原文的话说，他计算的是：`corpus-level clipped n-gram precision between sentence`，BLEU在corpus-level上是相当不错的评价指标，但是在sentence-level上不是十分适合。

  基础的BLEU公式为：
  

  $$CP_n(C,S)=\frac{\sum_i\sum_k\min(h_k(c_i),\max_{j\in m}{h_k(s_{ij})})}{\sum_i\sum_kh_k(c_i)}$$
  

  该式中，n-gram$\omega_k$是按照模型输出句子中的$\omega_k$来算的。(目前有点不太懂公式里面取最大最小的意义是什么？)

  上面的公式虽然可以计算模型输出句子中的n-gram的精确度，但是在输出句子不完整时容易造成误解。比如说，模型输出句子为："I am playing"，而参考句子为："I am playing basketball with my friends"，那么此时对于1-gram，模型输出的句子精确度将为1.0，因为模型输出的句子中的每个单词在参考句子中均有出现。为了应对这种情况，引入了brevity penalty：
  
  $$b(C,S)=\begin{cases}
      1 & l_C > l_S \\
      e^{1-\frac{l_C}{l_S}} & l_C \leq l_S
  \end{cases}$$
  
  加入brevity penalty后的BLEU计算公式为：
  
  $$BLEU_N(C,S)=b(C,S)exp(\sum_{n=1}^N\omega_nlogCP_n(C,S))$$
  
  其中,$N=1,2,3,4$，$\omega_n$是衡量n-gram之间相对重要性的权重。
  （但是为什么这里要对$CP_n$先取对数再取指数呢？）

+ ROUGE(Recall-Oriented Understudy for Gisting Evaluation)  
  ROUGE原本是用来衡量文本摘要算法性能的一系列指标。

  + $ROUGE_N$  
    由于是用来衡量文本摘要算法，自然地，模型的输出中包含了多少reference中的关键词是我们着重考虑的。$ROUGH_N$因此计算的是n-gram的召回率：


    $$ROUGE_N(c_i, S_i)=\frac{\sum_j\sum_k\min(h_k(c_i),h_k(s_{ij}))}{\sum_j\sum_kh_k(s_{ij})}$$

  + $ROUGE_L$  
    $ROUGE_L$是一种基于最长公共子序列(LCS，没要求一定是要连续的)的评价方法:
    

    $$R_l=\max_j\frac{l(c_i,s_{ij})}{|s_{ij}|}$$
    

    $$P_l=\max_j\frac{l(c_i,s_{ij})}{|c_i|}$$
    

    $$ROUGE_L(c_i,s_{ij})=\frac{(1+\beta^2) R_l P_l}{R_l+\beta^2 P_l}$$
    

    其中，$l(c_i,s_{ij})$表示$c_i$$s_{ij}$之间的最长子序列，$R_l$和$P_L$分别是精确率和召回率，$ROUGE_L$是他们的调和平均数。

  + $ROUGE_S$  
    $ROUGE_S$是基于skip bi-grams的一种评价指标。所谓skip bi-grams是指2-gram，但skip bi-gram中的两个单词不一定是要相邻的。比如，一个句子有4个单词，那么它的skip bi-grams则有$C_4^2=6$种。其计算公式如下：


    $$R_s=\max_j\frac{\sum_k\min(f_k(c_i),f_k(s_{ij}))}{\sum_k f_k(s_{ij})}$$


    $$P_s=\max_j\frac{\sum_k\min(f_k(c_i),f_k(s_{ij}))}{\sum_k f_k(c_i)}$$


    $$ROUGE_S(c_i,s_{ij})=\frac{(1+\beta^2) R_s P_s}{R_s+\beta^2 P_s}$$


+ METEOR(Metric for Evaluation of Translation with Explicit ORderin)  
  这个暂时还没搞懂(>_<)

  $$Pen=\gamma(\frac{ch}{m})^\theta$$
  
  $$F_mean=\frac{P_m R_M}{\alpha P_m + (a-\alpha) R_m}$$
  
  $$P_m = \frac{|m|}{\sum_kh_k(c_i)}$$
  
  $$R_m = \frac{|m|}{\sum_kh_k(s_{ij})}$$
  
  $$METEOR=(1-Pen)F_mean$$

+ CIDEr  
  这个也是还没搞懂(>_<)
  

  $$g_k(s_{ij})=\frac{h_k(s_{ij})}{\sum_{\omega\in\Omega}h_l(s_{ij})}\log(\frac{|I|}{\sum_{I_p\in I}\min(1,\sum_qh_k(s_{pq}))})$$


  $$CIDE_n(c_i, S_i)=\frac{1}{m}\sum_j\frac{\bold{g^n(c_i)}\cdot\bold{g^n(s_{ij})}}{\parallel \bold{g^n(c_i)} \parallel \parallel \bold{g^n(s_{ij})} \parallel}$$


  $$CIDER_r(c_i,S_i)=\sum_{n=1}^N \omega_n CIDEr_n(c_i, S_i)$$

## sdf