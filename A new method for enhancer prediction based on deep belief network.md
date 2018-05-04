# A new method for enhancer prediction based on deep belief network

## Abstract

这篇文章，作者提出一个新的基于`Deep Belief Network (DBN)`的计算模型用于增强子的预测，称之为EnhancerDBN.这个方法组合了多种特征，包括`DNA sequence compositional features`、`DNA methylation`和`histone modifications`.计算结果表明：1)EnhancerDBN在预测方面胜过13个现有的方法; 2）`GC content`和`DNA methylation `可用于增强子预测的相关特征。

## Background

* 目前计算方法存在的问题：
  * unsatisfactory prediction performance;
  * inconsistency of computational models across different cell-lines;
* EnhancerDBN训练数据集来自于`VISTAEnhancer Browser`，包含生物学验证的增强子样本;
* EnhancerDBN将预测问题转换为二分类任务，使用一个两步的方案：
  * first step：利用Restricted Boltzmann Machines (RBMs)构建DBN;
  * second step：基于深度神经网络分类器使用`back propagation (BP) `算法训练并优化DBN;

## Methods

### Datasets

* 增强子数据来自于[VISTA enhancer Browser ](http://enhan-cer.lbl.gov/),包含741个人类增强子;
* DNA序列数据和DNA甲基化数据是人类基因组（GRCh37/hg19）的汇编;
* 原始组蛋白修饰数据下载自`NIH Roadmap Epigenomics`；

| Dataset              | g Source               | Website                                                      |
| -------------------- | ---------------------- | ------------------------------------------------------------ |
| Enhancers            | VISTA enhancer Browser | <https://enhancer.lbl.gov/>                                  |
| DNA sequence         | UCSC                   | [http://hgdownload.soe.ucsc. edu/downloads.html#human](http://hgdownload.soe.ucsc.edu/downloads.html#human) |
| Histone modification | NIH Roadmap            | [http://www. roadmapepigenomics.org/](http://www.roadmapepigenomics.org/) |
| DNA methylation      | UCSC                   | [ http://genome.ucsc.edu/ cgi-bin/hgTables](http://genome.ucsc.edu/cgi-bin/hgTables) |
### The pipeline of EnhancerDBN

![Ct0VaD.png](https://s1.ax1x.com/2018/05/04/Ct0VaD.png)

#### 1.  Feature calculation

##### *1.1  DNA sequence compositional features*

* 使用`k-mers`作为序列的组成特性，使用k的范围在2~4;
* 某个k-mer和它的互补k-mer被作为是一个特征;
* 共得到4^2^/2+4^3^/2+4^4^/2=168个k-mer特征;
* 对于某个k-mer，计算它在每个正/负样本中的频率作为其对应的特征值;
* 另外，计算G和C出现在每个正/负样本中的总频率，作为GC含量特征值：

#### *1.2  DNA methylation feature*

* 使用每个样本的DNA甲基化水平作为DNA甲基化特征;
* DNA甲基化特征计算分为两步：
  * First，获得每个样本在基因组中的位置;
  * Then，根据它的位置，计算这个样本区域内甲基化的总值;

#### *1.3  Histone modification features*

* 这里使用了106类组蛋白修饰；
* 组蛋白修饰特征计算同样分为两步：
  * First，获得每个样本在基因组中的位置;
  * Then，根据其位置，对每一类组蛋白修饰，计算它在正/负样本区域内的总数;

### 2.  Constructing the EnhancerDBN classifier

下图描绘了EnhancerDBN分类器的结构，它包含了一个DBN和一个输出层。为了训练EnhancerDBN分类器，首先要以一个无监督的方式训练DBN；之后训练好的DBN进一步组合输出层形成DNN，这一步通过BP算法以监督的方式进行；最终得到EnhancerDBN分类器。

[![CtBVO0.md.png](https://s1.ax1x.com/2018/05/04/CtBVO0.md.png)](https://imgchr.com/i/CtBVO0)

#### *2.1  Training DBN with RBMs*

* DBN是一个多层的随机随机生成模型，通过训练一堆RBMs构建，每一个RBM的训练都是使用前一个RBM的隐层变量作为其可见变量;
* 这里构建的DBN具有3个RBMs，每一个RBM都有其自己的可见层和输出层;
* 在性能调优之后，分别将三个RBMs的隐层节点上数设置为50、50和200;
* 由于训练样本为276维向量，第一个RBM可见层节点数设置为276;
* 所以，由这三个RBMs相连构建的DBN具有276-50-50-200的结构;
* 对以RBMs为构建块的DBN执行一个贪婪的分层无监督的训练过程。训练过程如下:
  * Step 1. 将训练数据输入到1st RBM的可见层训练1st RBM;
  * Step 2. 将1st RBM的隐层作为2nd RBM的可见层训练2nd RBM;
  * Step 3. 将2nd RBM的隐层作为3rd RBM的可见层训练3rd RBM;
  * Step 4. 构建具有在三个RBNs中学习得到的权重和偏差的DBN;

#### *2.2  Training restricted Boltzmann machine (RBM)*

* 我们可以看到RBMs是一个接一个的训练，通过使用对比散度(`contrastive divergence`,CD算法)获得每个RBM的可见层和隐层之间的权重；
* 受限玻尔兹曼机（RBM）是随机神经网络模型的一种特殊类型，具有双层结构。一层被称为可见层，也可以称之为输入层；另一层被称为隐层。两层之间的节点是全连接的，但是在层内没有连接，这构成了一个二部图结构；

[![Ctszcj.md.png](https://s1.ax1x.com/2018/05/04/Ctszcj.md.png)](https://imgchr.com/i/Ctszcj)

* 这个联合结构的能量函数可以表示为：

  ![CtTX11.png](https://s1.ax1x.com/2018/05/04/CtTX11.png)

* 某个配置的联合概率分布由Boltzmann分布决定（这个配置的能量）：

  





