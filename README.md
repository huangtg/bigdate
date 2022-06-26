## 代码说明

### 环境配置

Python 版本：3.7.13
PyTorch 版本：1.11.0
CUDA 版本：11.3
操作系统：win10

所需环境在 `requirements.txt` 中定义。

### 数据

* 仅使用大赛提供的有标注数据（10万）。
* 未使用任何额外数据。


### 算法描述

* 首先将文本（title,asr,ocr）全部拼接起来，
* 经过一次embedding layer得到文本的embedding。
* 视频特征经过一个线性层+激活函数，映射到和文本embedding一样的维度（768），得到视频的embedding。
* 将文本和视频的embedding拼接起来，过一次BERT。
* 文本的embedding layer和BERT都是使用huggingface上已经预训练好的模型作为初始化，开源模型：https://huggingface.co/hfl/chinese-roberta-wwm-ext-large。
* 做一下mean pooling，使用一个MLP做200分类。
* 最后就是调学习率和epoch。



### 性能

离线测试性能：0.687
B榜测试性能：0.684472


### 训练流程

* 预训练初始化：bert 初始化权重来自于在中文语料预训练过的开源模型：https://huggingface.co/hfl/chinese-roberta-wwm-ext-large
  数据集：预训练使用了 pointwise 和 pairwise 集合，部分融合模型中加上了 test 集合。

* 模型的融合
  采用了 weighted concat -> svd 降维方法进行融合。
  最终的提交融合了五个模型。 
  模型都使用了 bert-large结构，各个模型加权权重均为 1/5。

### 测试流程

* 划分10%的数据作为验证集。取验证集上最好的模型来做测试。
