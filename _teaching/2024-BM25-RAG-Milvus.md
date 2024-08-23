
<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>
---
title: "BM25在RAG场景上的优化"
collection: teaching
type: "RAG-1"
permalink: /teaching/2024-RAG-1
venue: "杜新宇,中国联通AI创新中心"
date: 2024-08-22

location: "中国, 北京"
---

# BM25 for RAG with Milvus

## 1. TF-IDF

TF-IDF中TF代表词频，IDF代表逆词频。一个关键词x，在某个文档y中出现了多少次，即为词频TF(x,y)，词频越高则该文档y与关键词x越相关。公式如下：
$$
TF(x,y) = 关键词x在文档y中出现的次数
$$

IDF逆词频，度量的是关键词x，在整个文档库中以文档为单位出现的频率（在某个文档中出现多次算一次），出现的越多该词的重要性越低。如：“这，那，的，是”，这样的词在语料中出现的概率很高，因此，其逆词频值就很低，意味着这些词的重要程度比较低。公式如下：
$$
IDF(x)=log(\frac{文档总数}{包含关键词x的文档数})
$$


TF-IDF需要把TF和IDF两个值相乘，结果就是关键词x，与文档y的相关性分数。
$$
TF-IDF(x,y)=TF(x,y)*IDF(x)
$$
其中TF部分表征的是关键词和文档的相关程度，IDF部分表征的是关键词的重要性。如果一个query由若干个关键词[x_1, x_2, ..]组成，则最终文档y与该query的相关性得分为所有TF-IDF之和，即：
$$
score(query,y) = TF-IDF(x_1,y)+TF-IDF(x_2,y)+...\\
=TF(x_1,y)*IDF(x_1)+TF(x_2,y)*IDF(x_2)+..
$$
观察上式可以发现，其值为一系列乘积之和，这与向量内积形式一致。因此，可以表示为两个向量的内积形式。
$$
\overrightarrow{TF(y)}=[TF(x_1,y),TF(x_2,y),TF(x_3,y)...]\\
\overrightarrow{IDF(query)}=[IDF(x_1),IDF(x_2),...]
$$
上式中，两个向量的维度并不一致。TF向量的维度是文档y中包含词的个数。IDF向量维度是query包含词的个数。要想求内积，两个向量维度必须一致。因此，首先需要基于语料库构建词表向量。即整个语料库都有哪些词。并将上述两向量对应到词表上形成两个维度一致的稀疏向量。假设，整个语料库由5个词（x_1, x_2, x_3, x_4, x_5）组成，文档y包含其中的4个词(x_1, x_2, x_3, x_4), query包含其中两个词（x_1, x_5），则上述两个向量可以写为：
$$
\overrightarrow{TF(y)}=[TF(x_1,y),TF(x_2,y),TF(x_3,y),TF(x_4,y),0]\\
\overrightarrow{IDF(query)}=[IDF(x_1),0,0,0,IDF(x_5)]
$$
如上，将两个向量的维度就统一成词表的长度了。query和文档y的相关性得分即为这两个向量的内积：
$$
score(query,y)=\overrightarrow{TF(y)}\cdot\overrightarrow{IDF(query)}
$$

## 2. 标准BM25

### 2.1 词频饱和

考虑两种情景：情景一，有一个关键词，在文档A中出现了2次，在文档B中出现了4次，在A和B长度相同的情况下，B的TF值是A的2倍。情景二，关键词在文档A中出现了200次，在文档B中出现了400次。在A和B长度相同的情况下，B的TF值也是A的2倍。然而，直观上认为在情景二，文档A和B的相关性应该是差不多的，关键词都出现了许多次。这就涉及到词频**饱和**的问题。即希望相关性和词频不是成线性关系，是成某种饱和曲线的关系。所以，BM25算法对TF部分做如下改动：
$$
TF(x,y)=\frac{TF(x,y)}{TF(x,y)+k}
$$
### 2.2 文档长度

BM25对于TF-IDF的另外一部分改动与文档**长度**相关。TF部分只考虑了词频没有考虑文档的长度。考虑两个文档A和B，A有10个词，关键词x出现1次，B有10000个词，关键词x出现10次。显然文档A的相关度大于文档B。但是其TF值只有B的十分之一。因此，BM25对TF部分做了进一步的改进：
$$
TF(x,y)=\frac{TF(x,y)}{TF(x,y)+k*\frac{D(y)}{avg(D)}}
$$
其中D为文档y的长度，avg(D)是语料库中所有文档的长度均值。

该公式看似合理，但是也有一定的弊端，在不同的语料库中，文档长度对TF值的影响是不同的。有的语料对长度敏感，而有的语料对长度不敏感。所以，BM25算法对长度影响部分加入了权重b，改进公式如下：
$$
TF(x,y)=\frac{TF(x,y)}{TF(x,y)+k*(1-b+b*\frac{D(y)}{avg(D)})}
$$
上式中，b=0则长度影响部分则完全不被考虑。

BM25也对IDF部分做了改进：
$$
IDF(x)=log(\frac{N-DF(x)+0.5}{DF(x)+0.5}) \qquad if \qquad IDF(x)\geq 0 \\
IDF(x)=\epsilon*avg(IDF)  \qquad if \qquad IDF(x)<0
$$
式中N为语料中文档总数，DF(x)为包含关键词x的文档数。如该值为负，则用词表上所有IDF的均值乘以一个参数当做IDF值，该参数经验值为0.25。

## 3. 近似BM25

当语料库构建完毕后，通过BM25算法可以构建每个文档的TF向量，然后存入Milvus向量数据库中。并且词表中每个词的IDF值也可以提前计算好，存入BM25模型中。用户查询时，首先将用户query进行分词。然后将每个词的IDF值查出来并构成IDF向量。用该向量与向量数据库中的TF向量做内积(IP,inner product)。内积值即为query和文档的相关性。

### 3.1 动态语料库

当有新文档添加到语料库时，对TF和IDF值都有影响，如TF部分的平均文档长度会改变，IDF部分的N值会增加，甚至词表也会有改变比如出现了新的词。这就导致已经存储在milvus向量库中的文档TF向量全部需要重新生成更新。所有的IDF值也得重新计算。这个流程在每次添加新文档时都要进行一遍，其时间成本非常高。

因此，有必要对标准的BM25算法做一些近似，以提高效率。在RAG场景下，文件都是按照chunksize拆分为文本块的，文本块就是语料库中的文档。因此可以用chunksize代替avg(D)，使得该值在插入新的文档后不变。这样，已经存储的TF向量就不用重新构建了。词表和词表的IDF向量是需要重建的，但只需要把词表中相关的词的词频加一，再把N值加一，重新计算即可，计算耗时很少。
$$
TF(x,y)=\frac{TF(x,y)}{TF(x,y)+k*(1-b+b*\frac{D(y)}{chunksize})}
$$

### 3.2 增量构建流程

1.对新增文档分词然后去重

2.每个词在词频向量中查询，如有则其值加一，没有则扩充词表，词频设为一。

3.文档总数N加一

4.重新计算IDF向量