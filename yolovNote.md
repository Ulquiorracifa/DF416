# 视觉任务：

目标检测：框选区域定位 （回归问题更简单，方便图片的内容描述）

图像分割：像素级的定位 （标 签困难 往往更耗时 但选区更具意义）





![è®¡ç®æºè§è§ä""å¡](https://img-blog.csdn.net/20180130220548650?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGlhb2h1MjAyMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



先考虑目标检测问题：

1.R-CNN Fast R-CNN, Faster R-CNN 		先产生proposal region 再对区域进行分类（通过）

2.yolo，SSD							



#### YOLO

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20160317163739691)



yolo：通过每个 bounding box预测以中心落在这个box内的物体的dx dy dw dh以及confidence（或者无物体中心  ，此时Pr（Object）取0， 存在则取1）

​				![img](https://img-blog.csdn.net/20160317154752063)

同时要预测每个网格的类别（由类别数C位组成）

###### 网络骨架

借鉴Googlenet，24个卷积层，2个全链接层

![img](https://img-blog.csdn.net/20160317155955917)



从对每个C类的预测值与Confidence的乘积返回每个类的概率

用nms过滤bbox



###### 关键问题

1.x,y,d,w归一到0-1之间

2.在均方差设计的损失函数中加入权值来防止对大量出现的无object的网格。λnoobj=0.5，λcoord=5

3.w,h比x,y更敏感，loss中使用平方根

4.每个目标仅用一个box来预测，由box之间的IOU来判断哪个来负责



![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20160317163417800)

