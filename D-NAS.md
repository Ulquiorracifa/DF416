## **proxyless NAS**





![1556603389718](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556603389718.png)

不使用proxy	直接从框架中学习，移除重复块；新的基于梯度方法；



### Method

- ##### NAS 的路径级减枝视角

  预构建一个N (e; · · · ; en)  :网络类型，O = {oi}:操作类型，mO(x)  为 混合操作的输出（N条路径）

  ![1556604619415](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556604619415.png) 如下图：

  ##### ![1556604668117](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556604668117.png)

  ​		通过框架参数（Architecture Parameters）学习每个位置合适的操作，通过二值开关来选择操作类型。αi为框架参数 

  ![1556605017836](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556605017836.png)

  

- ##### 二值化路径

  当然不能并行的训练所有候选操作。通过二值化框架参数，只激活一条路径。训练过程中，使用**BinaryConnect**。依靠BinaryGate 的梯度来更新框架参数。

  ![1556605414974](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556605414974.png)



- ##### 优化不可导硬件指标

  ###### 网络的延时模拟成关于网络规模的连续函数

  ![1556608125813](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556608125813.png)

  

  LossCE 为cross-entropy loss 

  ###### 使用REINFORCE训练二值化权重

  Williams1992_Article_SimpleStatisticalGradient-Following Algorithms for
  Connectionist Reinforcement Learning

  ![1556610065331](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556610065331.png)

- #### 实验结果



![1556610520795](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556610520795.png)



得到的各个平台的网络框架

![1556610626007](C:\Users\fourt\AppData\Roaming\Typora\typora-user-images\1556610626007.png)



在三个硬件平台上搜索到的 CNN 模型的详细结构：GPU / CPU / Mobile。我们注意到，当针对不同平台时，网络结构呈现出不同的偏好：（i）GPU 模型短而宽，尤其是在 feature map 较大时;（ii）GPU 模型更喜欢大 MBConv 操作（例如 7x7 MBConv6），而 CPU 模型则倾向于小操作。这是因为 GPU 比 CPU 有更高的并行度，因此它可以更好地利用大 MBConv。另一个有趣的观察是，当特征地图被下采样时，所有的网络结构都倾向于选择一个更大的 MBConv。我们认为这可能是因为大 MBConv 操作有利于网络在下采样时保留更多信息。值得注意的是，这是之前强制 block 之间共享结构的 NAS 方法无法发现的。