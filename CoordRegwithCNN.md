---
typora-root-url: pic 
---





#### Main i dea

###### 常用的：

- 全连接层（缺泛化）

  全连接层会丢失空间一般化特性

  CNN（带全连接层）→FCN（不带全连接层）

  而FCN存在空间一般化特性和端到端特性

  本文的DSNT层把FCN的两个特性和 坐标定位空间热图结合。

- 高斯热图（非全微分）



##### DSNT：

###### 解决

- 全连接破坏空间，过拟合
- 高斯热图输出维度大；存在理论下届；MSE LOSS学习出偏移；不是全微分

**本文设计和目前高斯热图预测主要区别是我们并不是强制要求一个loss直接作用到高斯预测热图上**

通过优化整个模型输出的预测坐标的损失来间接学习热图，也就是loss是基于预测关键点和真实关键点的

->(batch,h,w,3)->(batch,h/4,w/4,17):17对关键点回归->(batch,17,2):17对关键点的(x,y)



###### 从热图到(x,y)坐标转换的方式：

![1558704831859](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/1558704831859.png)



其中，X，Y的分布由：

![1558705065285](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/1555936928375.png)

![1558705076923](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/1558705076923.png)

得到。其实就是期望均值到分布位置。



#### loss function

![1558705426334](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/1558705426334.png)

而后可以加入正则项帮助并行监督热图回归。（方差正则、KL散度，Jensen-Shannon）



##### 试验问题：

resize固定倍率->怎么放入input













#### FCN与CNN差别：

全卷积网络(FCN)是从抽象的特征中恢复出每个像素所属的类别。即从图像级别的分类进一步延伸到像素级别的分类。通过替换网络结构最后的全连接层，将最后的卷积层结果通过上采样，实现对每个像素位置的预测（语义分割预测）



###### ![pascal数据集的FCN网络结构](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/20160508234037674.png)

​	 虚线下的内容：从不同阶段的的几个预测层中联合预测定位每个像素点的分类结果。（上采样存在裁剪）最后的卷基层输出转化为对每个类别的可能性。然后从这类别数张预测图中找argmax，就是该位置的最大类别可能性。



##### 上采样过程：

![反卷积-上采样过程](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/20160510150910165.png)

stride表示步长。



###### 在训练过程中，从最后一层输出开始，一步步融入上一个pooling的预测结果来训练上采样提取细节。

![FCN不同倍上采样分析结果](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/20160511111507947.png)



##### 存在问题：



1. FCN-8s仍然不够精细（有待提升）
2. 空间之间的相互分布关系（大步长每个点阵之间的关系较小，点阵内的预测通过上采样分布倒是一致 ）

#### 



#### Faster RCNN

##### ROI pooling:

​	大量的固定bouding boxs特征图使得检测困难。



ROI pooling具体操作如下：

（1）根据输入image，将ROI映射到feature map对应位置；

（2）将映射后的区域划分为相同大小的sections（sections数量与输出的维度相同）；

（3）对每个sections进行max pooling操作；

这样我们就可以从不同大小的方框得到固定大小的相应 的feature maps。值得一提的是，输出的feature maps的大小不取决于ROI和卷积feature maps大小。ROI pooling 最大的好处就在于极大地提高了处理速度



#### RetinaNet：

![RetinaNet_Struct](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/20180422141556616.png)







##### RoI Align（Mask R-CNN）:

##### ROI Pooling局限性：

1.将候选框边界量化为整数点坐标值。2.将量化后的边界区域平均分割成 k x k 个单元(bin),对每一个单元的边界进行量化

预选框位置由模型回归得到，为浮点坐标，但中间的ROI pooling存在两次量化误差。称为“不匹配问题”。**misalignment**

[（例如目标大小665，图片大小800）由步长截取的物体已存 在由整数坐标（665/stride(32)）化为小数（20.78），而框选区域化整了小数坐标（20）；框内特征（20）池化一定大小（7*7），将输入的目标图（20/7）量化到整数（2）]

##### ROI Align 思想：

取消量化操作，使用双线性内插的方法获得坐标为浮点数的像素点上的图像数值,从而将整个特征聚集过程转化为一个连续的操作。

- 遍历每一个候选区域，保持浮点数边界不做量化。
- 将候选区域分割成k x k个单元，每个单元的边界也不做量化。
- 在每个单元中计算固定四个坐标位置，用双线性内插的方法计算出这四个位置的值，然后进行最大池化操作。

![RoIAlign-featureMap](https://raw.githubusercontent.com/Ulquiorracifa/DF416/master/pic/5a168b96ab6441421e0026bd.png)

