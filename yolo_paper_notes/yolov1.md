# yolov1目标检测算法

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



















