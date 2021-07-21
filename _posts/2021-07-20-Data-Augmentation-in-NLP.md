---
layout: post
title: NLP领域中的数据增强
categories: [Data Augmentation]
tags: [NLP, Data Augmentation]
description: 领域增强技术汇总
---

## 写在前面
寻寻觅觅，不知道什么时候收藏的一篇NLP领域的数据增强的github地址[^1]，今天既然翻到了，那就整理一下吧。

## 不是开始的开始
翻开以后，好大一篇readme，英语没过六级的我，看的头疼，抛砖引玉，先看一部分，后续的等后续有时间再看吧。『Visual Survey』[^2]看起来应该是有图解的，就从他开始吧。

阅读的过程中发现了个宝藏，好像是Google给出的数据增强的一些方案。[^3]

另外一些增强方案。[^5]

## A Visual Survey of Data Augmentation in NLP
cv领域中数据增强是一个标准的流程步骤，可能与数据的分布特点有关，NLP领域中数据增强当前的发展还比较原始。

### 方法介绍
#### 词汇替换（Lexical Substitution）
目标是在不改变句子语义的前提下，替换句子中的部分词汇，从而达到数据增强的目的。

1. 同义词替换：使用同义词替换句子中的一个词汇。依赖同义词词典，依赖分词工具。
2. 基于词向量的替换：使用最近的词向量替换句子中的词汇。
3. 基于MLM的替换：mark掉需要替换的词汇，通过MLM预测其概率最大的值。主要的难点在于如何启发式地确定需要mask的文本。
4. 基于TF-IDF的替换：具有较低TF-IDF值的词汇，具有较低的信息量，可以直接相互替换。

#### 回译（Back Translation）
A语言的句子翻译为B语言，然后再翻译回A语言。可以通过多个语言对，来实现一对多的数据增强。

#### 简单模式匹配变换的数据增强（Text Surface Transformation）
主要为英语语种的缩写，以及扩写。[^4]

#### 随机噪声数据增强（Random Noise Injection）
该类方法的主要思想是在文本中注入噪声，从而使得模型学习到的东西更加健壮。
1. 注入拼写错误噪声：[github history commit](https://github.com/makcedward/nlpaug/blob/5238e0be734841b69651d2043df535d78a8cc594/nlpaug/res/word/spelling/spelling_en.txt)
2. 基于键盘布局的随机噪声：随机替换字符为键盘上相邻的字符。
3. 基于词汇在语料库中出现的频率的随机替换：需要统计语料库中的单个词的词频。
4. 空白噪声：将单词替换为一个占位符。
5. 句子乱序：以句子为独立粒度，将原始的句子打乱后输出。
6. 随机插入：首先找到一个非停用词，然后找到停用词的同义词，最后将同义词随机插入到原句中。
7. 随机替换：随机替换句子中的两个位置的词。可以认为是句子乱序的一种特殊情况。

#### 样本交叉数据增强（Instance Crossover Augmentation）
论文中的方案是，对分类问题，同一个类别下的任意两个样本。将其分别切分为前后两部分后，交换其中一部分，然后生成两个新的句子。其基本的假设是：类别信息不依赖句子的完整 & 连通性。主要用于情感分类的样本。

#### 基于句法树的数据增强（Syntax-tree Manipulation）
根据句法规则转换原始样本，例如将主动句转换为被动句。

#### 文本混杂（MixUp for Text）

参考图片混杂的方案，开发出来的文本混杂方法。
1. 词粒度混杂：两个句子经过padding后，按位置对词向量进行混杂。
2. 句子粒度混杂：词向量经过编码为句向量后，对句向量经过混杂产出混杂embedding。

#### 基于数据生成的方法（Generative Methods）
当前主要面向的是分类问题。

1. 有条件的预训练语言模型：首先基于分类样本，拼接上label后，finetune一个预训练语言模型，然后通过带有label的样本前缀，生成完整的样本。


### 一个可行的实现方案的介绍
可以基于第三方工具包：[nlpaug](https://github.com/makcedward/nlpaug) or [textattack](https://github.com/QData/TextAttack) 来进行功能实现。

## 参考
[^1]: https://github.com/styfeng/DataAug4NLP
[^2]: https://amitness.com/2020/05/data-augmentation-for-nlp/
[^3]: https://github.com/google-research/uda/tree/master/text/augmentation
[^4]: https://github.com/kootenpv/contractions 
[^5]: https://github.com/makcedward/nlpaug#quick-demo