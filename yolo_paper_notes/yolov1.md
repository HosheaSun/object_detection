# yolov1目标检测算法
# 1.网络架构

## 【R-CNN系列不足】

R-CNN系列算法（R-CNN、SPPNet、Fast R-CNN、Faster R-CNN）均是采用two-stage的方法

（1.提取region proposal 2.分类+边框回归），

主要是对region proposal进行识别定位。虽然这类方法检测精度很高，但由于需要一个单独的网络进行提取region proposal，因此在速度上无法突

破瓶颈。

## 【YOLOv1创新点】

1.将detection视为回归问题，仅使用一个neural network同时预测bounding box的位置和类别，因此速度很快。

2.由于不需提取region proposal，而是直接在整幅图像进行检测，因此YOLOv1可以联系上下文信息和特征，减少将背景检测为物体的错误。

3.YOLOv1学习到的是目标的泛化表示（generalizable representations），泛化能力非常强，更容易应用于新的领域或输入。

## 【算法流程】

由于不需提取region proposal，则YOLOv1的检测流程很简单：

Resize image：将输入图片resize到448x448。

Run ConvNet：使用CNN提取特征，FC层输出分类和回归结果。

Non-max Suppression：非极大值抑制筛选出最终的结果。

没有region proposal，那应该怎么定位那些包含目标的区域并固定输出呢？简单来说，YOLOv1的做法是：CNN网络将resize后的图像分割成S x S（7 x 7）的单元格，若目标的中心点落在某一单元格，则该单元格负责检测该目标，输出该目标的类别和边框坐标。例如：下图中狗的中心落在红色单元格内，则这个单元格负责预测狗。

## 【网络结构】

![image](https://user-images.githubusercontent.com/95267383/202628848-688d6e74-481e-4965-8f42-4bec5ee35d44.png)


# 2. 训练的trick

首先在ImageNet上对网络中的前20层进行预训练，之后再在这20层后连上4层卷积和2层全连接层进行训练。所以，前20层是用预训练网络初始化，最后的这6层是随机初始化的并在训练过程中更新权重。此外，因为detection需要更多图片细节的信息，所以在训练时，统一将输入图片的size从调整为。对于loss函数，是通过ground truth和输出之间的sum-squared error进行计算的，所以相当于把分类问题也当成回归问题来计算loss

# 3.损失函数

![image](https://user-images.githubusercontent.com/95267383/202629121-8533e854-7f1e-4297-957b-eab917efdcf7.png)

## 1.位置误差

## 2.置信度误差

## 3.分类误差



# 4.测试

![image](https://user-images.githubusercontent.com/95267383/202629532-6997dcda-28e4-451b-b4f1-4f2ab906c0c5.png)

![image](https://user-images.githubusercontent.com/95267383/202629545-9871bb63-4b47-478b-b14c-b5fe46d0b15f.png)

根据同样的方法可以计算得到7 x 7 x 2 = 98个bbox的confidence score，然后根据confidence score对预测得到的98个bbox进行非极大值抑制，得到最终的检测结果。NMS参考Jacqueline大佬的文章【目标检测】基础知识：IoU、NMS、Bounding box regression。

# YOLOv1缺点

因为YOLO中每个cell只预测两个bbox和一个类别，这就限制了能预测重叠或邻近物体的数量，比如说两个物体的中心点都落在这个cell中，但是这个cell只能预测一个类别。
此外，不像Faster R-CNN一样预测offset，YOLO是直接预测bbox的位置的，这就增加了训练的难度。
YOLO是根据训练数据来预测bbox的，但是当测试数据中的物体出现了训练数据中的物体没有的长宽比时，YOLO的泛化能力低
同时经过多次下采样，使得最终得到的feature的分辨率比较低，就是得到coarse feature，这可能会影响到物体的定位。
损失函数的设计存在缺陷，使得物体的定位误差有点儿大，尤其在不同尺寸大小的物体的处理上还有待加强。

## 参考文章：

https://zhuanlan.zhihu.com/p/115759795

https://zhuanlan.zhihu.com/p/63507794

https://zhuanlan.zhihu.com/p/46691043

https://zhuanlan.zhihu.com/p/24916786

https://zhuanlan.zhihu.com/p/32525231




