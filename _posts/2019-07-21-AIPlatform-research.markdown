---

layout:     post
title:      "AIPlateform-Research"
author:     "JiG"
header-img: "img/post-bg-2015.jpg"
tags: 

  - summary

---

# **Inception_v1_v2**

## **Introduction** 
2014年，GoogLeNet获得当年ImageNet挑战赛(ILSVRC14)的第一名、GoogLeNet虽然深度只有22层，但大小却比AlexNet和VGG小很多，
GoogleNet参数为500万个，AlexNet参数个数是GoogleNet的12倍，VGGNet参数又是AlexNet的3倍，因此在内存或计算资源有限时，GoogleNet是比较好的选择；
从模型结果来看，GoogLeNet的性能却更加优越。 Inception系列就是GoogLeNet团队设计的一种 “基础神经元”结构，搭建了一个稀疏性、高计算性能的网络结构。
本文档着重介绍inception_v1和inception_v2。

## **Inception_v1**
最原始的inception结构如图所示，该结构将CNN中常用的卷积（1x1，3x3，5x5）、池化操作（3x3）堆叠在一起（卷积、池化后的尺寸相同，将通道相加），一方面增加了网络的宽度，另一方面也增加了网络对尺度的适应性。
不同尺寸的卷积层既能够提取输入特征的细节信息又可以增加特征提取的感受野，同时，池化操作用以减少空间大小，降低过度拟合。

<img src='/img/ai-imgs/inception_1.png' width="600px"/>

然而Inception原始版本中，所有的卷积核都在上一层的所有输出上来做，而那个5x5的卷积核所需的计算量就太大了，造成了特征图的厚度很大，为了避免这种情况，在3x3前、5x5前、max pooling后分别加上了1x1的卷积核，以起到了降低特征图厚度的作用，这也就形成了Inception v1的网络结构，如下图所示：

<img src='/img/ai-imgs/inception_2.png' width="600px"/>

1x1卷积核的作用：1x1卷积的主要目的是为了减少维度，还用于修正线性激活（ReLU）。比如，上一层的输出为100x100x128，经过具有256个通道的5x5卷积层之后(stride=1，padding=2)，输出数据为100x100x256，其中，卷积层的参数为128x5x5x256= 819200。而假如上一层输出先经过具有32个通道的1x1卷积层，再经过具有256个输出的5x5卷积层，那么输出数据仍为为100x100x256，但卷积参数量已经减少为128x1x1x32 + 32x5x5x256= 204800，大约减少了4倍。
基于Inception_v1构建了拥有22层的GoogLeNet，由于网络结构图过于庞大，如若感兴趣请查看[官方论文](http://arxiv.org/abs/1409.4842).

## **inception_v2**
//大尺寸的卷积核可以带来更大的感受野，但也意味着会产生更多的参数，比如5x5卷积核的参数有25个，3x3卷积核的参数有9个，前者是后者的25/9=2.78倍。因此，GoogLeNet团队提出可以用2个连续的3x3卷积层组成的小网络来代替单个的5x5卷积层，即在保持感受野范围的同时又减少了参数量，如图所示：

<img src='/img/ai-imgs/inception_5.png' width="200px"/>

接着，GoogLeNet又考虑是否可以以更小的卷积操作来代替3x3卷积呢，因此，他们又设计出如图下图所示的卷积，即用3个3x1代替一个3x3卷积。

<img src='/img/ai-imgs/inception_6.png' width="200px"/>

因此，任意nxn的卷积都可以通过1xn卷积后接nx1卷积来替代。

# **MobileNet**

## **Introduction**
MobileNet是为移动端和嵌入式端深度学习应用设计的网络，使得网络结构在cpu上也能达到理想的速度要求。

## **MobileNet结构**
MobileNet网络结构如图所示：

<img src='/img/ai-imgs/MobileNet_1.png' width="400px"/>

## **MobileNet的过人之处**

* **MobileNet网络结构的特点**
轻量化，用步长为2的卷积层代替了池化层。
* **MobileNet的创新处**
网络模型设计出一种新的卷积操作：深度可分离卷积层。如下图所示，两者的差别在于：标准卷积的卷积核的通道数等于输入特征图的通道数，而深度可分离卷积层分为两个部分，第一个部分叫depth-wise层，它的卷积核的通道数为1；第二部分叫point-wise层，它是又1x1的卷积核构成，通道数即为输入特征图的通道数。我们可以看到MobileNet在depthwise卷积和pointwise卷积后都各有BN和ReLU操作。

<img src='/img/ai-imgs/MobileNet_3.png' width="400px"/>

* **超参数**
MobileNet用两个超参数来控制网络计算速度与准确度之间的平衡。

**Width Multiplier α: Thinner Models：** 所有层的 通道数（channel） 乘以 α 参数(四舍五入)，模型大小近似下降到原来的 α2倍，α∈(0,1] ] with typical settings of 1, 0.75, 0.5 and 0.25，降低模型的宽度

**Resolution Multiplier ρ: Reduced Representation：** 输入层的 分辨率（resolution） 乘以 ρ 参数 (四舍五入)，等价于所有层的分辨率乘 ρ，模型大小不变，计算量下降到原来的ρ2 倍，ρ∈(0,1]，降低输入图像的分辨率。


# **ResNet**

## **Introduction**
ResNet由微软研究院的何凯明等4名华人提出，通过使用Residual Unit成功训练152层深的神经网络，在ILSVRC 2015比赛中获得了冠军，取得3.57%的top5错误率，同时参数量却比VGGNet低，效果非常突出。ResNet的结构可以极快地加速超深神经网络的训练，模型的准确率也有非常大的提升。本文档着重介绍ResNet的50层算法结构。

## **Residual Unit**
ResNet由多个Residual Unit（残差模块）构成。正是由于残差模块的引入，使得ResNet可以有很深的卷积层，它通过减轻神经网络学习内容来达到这一点。假定某段神经网络的输入是x，期望输出是H(x)，一般的做法是直接使用卷积层来无限逼近映射关系，而残差模块则是学习F(x) = H(x) – x，即残差模块分担了学习任务，只需要学习F(x)的内容，而后直接与输入x相加，形成最后期望的输出值H(x) = F(x) + x。如下图所示，残差模块相当于将学习目标改变了，不再是学习一个完整的输出H(x)，只是输出和输入的残差H(x)-x。 

<img src='/img/ai-imgs/resnet_1.png' width="400px"/>

## **ResNet_v1_50**
接下来，我们介绍ResNet_v1_50的网络结构。ResNet不管是多少层的结构，都分成四个主要层（不算起初的7x7卷积层和3x3最大池化层），如下图红框内即为50层结构。

<img src='/img/ai-imgs/resnet_2.png' width="400px"/>

我们依次介绍每一层，首先如下图所示：首先是7x7卷积层和3X3的最大池化层。如下图所示，图中（7x7，64，stride=2,padding=3）表示卷积核大小为7x7，卷积核数量为64，卷积步长为2，padding大小为3。同理，（3x3，stride=2,padding=3）表示池化方法使用的3x3的卷积核，步长为2，padding大小为3。

<img src='/img/ai-imgs/resnet_3.png' width="400px"/>

接着，如下图所示，开始了ResNet的关键结构。红框内即为Residual Unit（残差模块），每一个残差模块包含了三个卷积操作，conv2_x则共有三个相同的残差模块，需要注意的是，在我们进行残差相加的时候，需要对初始的输入（64x56x56）做一个扩围操作，它是一个（1x1，256，stride=1,padding=1）的卷积操作。

<img src='/img/ai-imgs/resnet_4.png' width="400px"/>

接下来是conv3_x，如下图所示，它也使用了包含三个卷积层的残差模块，不同于conv2_x有三点：（1）残差模块的卷积核数量不同；（2）残差模块的数量不同，即conv3_x是四个相同的残差模块；（3）在conv3_x中，由于第一个残差模块的输入维度（256x56x56）跟它的输出维度（512x28x28）不同，需要给输入的特征做一个下采样的卷积操作。

<img src='/img/ai-imgs/resnet_5.png' width="400px"/>

接下来的conv4_x和conv5_x都是类似的说明，此处不再赘述。

# **VGG**

## **Introduction**
VGG模型是2014年ILSVRC竞赛的第二名，第一名是GoogLeNet。

## **VGG的特点**
VGG网络结构如下图所示：

<img src='/img/ai-imgs/VGG_1.png' width="400px"/>

VGG的特点如下：

（1）结构简洁：VGG由5层卷积层、3层全连接层、softmax输出层构成，层与层之间使用max-pooling（最大化池）分开，所有隐层的激活单元都采用ReLU函数；

（2）小卷积核和多卷积子层：VGG使用多个较小卷积核（3x3）的卷积层代替一个卷积核较大的卷积层，一方面可以减少参数，另一方面相当于进行了更多的非线性映射，可以增加网络的拟合/表达能力；

（3）小池化核：相比AlexNet的3x3的池化核，VGG全部采用2x2的池化核；

（4）通道数多：VGG网络第一层的通道数为64，后面每层都进行了翻倍，最多到512个通道，通道数的增加，使得更多的信息可以被提取出来；

（5）层数更深、特征图更宽：由于卷积核专注于扩大通道数、池化专注于缩小宽和高，使得模型架构上更深更宽的同时，控制了计算量的增加规模；

（6）全连接层转卷积层（测试阶段）：在网络测试阶段将训练阶段的三个全连接替换为三个卷积，使得测试得到的全卷积网络因为没有全连接的限制，因而可以接收任意宽或高为的输入，这在测试阶段很重要。如图一所示，输入图像是224x224x3，如果后面三个层都是全连接，那么在测试阶段就只能将测试的图像全部都要缩放大小到224x224x3，才能符合后面全连接层的输入数量要求，这样就不便于测试工作的开展。

而“全连接转卷积”可以解决这一问题，替换过程如下图所示：

<img src='/img/ai-imgs/VGG_2.png' width="400px"/>

## **VGGNet结构**

VGGNet的结构如下图所示，它共有五个结构相似，差异不大的网络模型。

<img src='/img/ai-imgs/VGG_3.png' width="400px"/>

 以VGG16为例，简化其网络结构图如下：

 <img src='/img/ai-imgs/VGG_4.png' width="400px"/>

 
