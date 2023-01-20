# Lecture1

> Language isn't a formal system, language is glorious chaos.

## Traditional NLP problems

1. In traditional NLP, we regard word as discrete symbols. 意味如果我们使用one-hot对单词进行编码，我们得到的vector dimension = number of words in cocabulery. 这样的问题是每一个单词都是离散的，我们不能学到synonyms and get similarity（任意one hot词向量的表示是正交的，没有任何相似性可言,one hot 是一种local representation）。
2. 针对上面的这个问题，我们使用Distributional semantics：A word's meaning is given by the words that frequently appear close-by. 意思是一个单词的语义有上下文决定。when a word w appears in a text, its context is the set of words that appear near-by(within a fixed window). **And we use the many contexts of w to build up a representation of w.**我们将one hot这种稀疏的变量转换为了dense vector，这种方法我们称为word vectors, word embeddings or neural word representations (**是一种distributed representation**)，一般来说，这些dense vector的dimension为300维，这样相较于one hot的表示减少了维度。Word2vec 本质上是一种**降维**操作——把词语从 one-hot encoder 形式的表示降维到 Word2vec 形式的表示。



## Word2vec

Word2Vec是语言模型中的一种，它是从大量文本预料中以无监督方式学习语义知识的模型，被广泛地应用于自然语言处理中。Word2Vec是用来生成词向量的工具。

![word2vec在nlp中的位置](https://easyai.tech/wp-content/uploads/2022/08/1ece2-2020-02-17-w2v-guanxi.png)

### Overview

- we have a large corpus("body") of text
- Every word in a fixed vocabulary is represented by a vector
- Go through each position *t* in the text, which has a center word *c* and context("outside") word *o*
- Use the similarity of the word vectors for *c* and *o* to calculate the probability of *o* given *c* (or vice versa)
- Keep adjusting the word vectors to maximize this probability 

![image-20230120121844013](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202301201218700.png)

根据图可以了解*c，o*的具体含义，还要考虑window of size的影响

### Objective function

![image-20230120122356134](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202301201223436.png)

在已知了objective function后，式子里面我们就只需要计算出$P(w_{t+j}|w_t;\theta)$就可以了，为了简化计算和方便优化，we will use two vectors per word *w*

- $v_w$: when w is a center word
- $u_w$: when w is a context word

Then for a center word *c* and a context word *o*:
$$
P(o|c) = \frac{exp(u^T_ov_c)}{\sum_{w\in V} exp(u^T_wv_c)}
$$
softmax函数输出的是一个概率分布，dot production代表的是点乘的相似度

![image-20230120153706073](https://niuzhikang.oss-cn-chengdu.aliyuncs.com/figures/202301201537228.png)

$\theta$代表的是模型的参数，模型的参数有2dV个，V是词表的大小，d是dimension。

Word2vec是一个简单的神经网络(下方skip gram的模型图)，输入是one-hot vector，hidden layer没有激活函数，也就是线性的单元。output Layer维度跟Input Layer的维度一样，用的是Softmax回归。我们要获取的dense vector其实就是hidden layer的输出单元。有的地方定为input layer和hidden layer之间的权重，其实说的是一回事。

word2vec主要分为CBOW（Continuous Bag of Words）和Skip-Gram两种模式。

- 如果是用一个词语作为输入，来预测它周围的上下文，那这个模型叫做『Skip-gram 模型』
- 而如果是拿一个词语的上下文作为输入，来预测这个词语本身，则是 『CBOW 模型』

CBOW对小型数据库比较合适，而Skip-Gram在大型语料中表现更好。对同样一个句子：Hangzhou is a nice city。我们要构造一个语境与目标词汇的映射关系，其实就是input与label的关系。
这里假设滑窗尺寸为1（滑窗尺寸……这个……不懂自己google吧-_-|||）
CBOW可以制造的映射关系为：[Hangzhou,a]—>is，[is,nice]—>a，[a,city]—>nice
Skip-Gram可以制造的映射关系为(is,Hangzhou)，(is,a)，(a,is)， (a,nice)，(nice,a)，(nice,city)



### Skip gram

![Skip-gram用当前词来预测上下文](https://easyai.tech/wp-content/uploads/2022/08/514ce-2019-09-26-Skip-gram.png)

![skip gram](https://pic4.zhimg.com/v2-a1a73c063b32036429fbd8f1ef59034b_r.jpg)

### CBOW

![CBOW通过上下文来预测当前值](https://easyai.tech/wp-content/uploads/2022/08/320cb-2019-09-26-cbow.png)

![img](https://img2020.cnblogs.com/blog/1365470/202107/1365470-20210720001356525-172859390.png)



### 优化方法

1. Negative Sample
2. Hierarchical Softmax



## 参考链接

1. [[NLP] 秒懂词向量Word2vec的本质](https://zhuanlan.zhihu.com/p/26306795)
2. [大白话讲解word2vec到底在做些什么](https://blog.csdn.net/mylove0414/article/details/61616617)
3. [Word2Vec原理详解 ](https://www.cnblogs.com/lfri/p/15032919.html)
4. [通俗易懂word2vec详解，入门级选手无难度](https://blog.csdn.net/bitcarmanlee/article/details/82291968)
5. [Word2Vec Tutorial - The Skip-Gram Model](http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/)