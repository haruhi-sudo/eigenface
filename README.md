# Python实现的EigenFace识别人脸程序(两种方式)

Python版本：3.8，依赖库：numpy

```python
结构
│  
└───yalefaces# 用于训练和测试的文件，将数据集分为了10折, 每次拿出1折测试, 9折训练
│   └───train0
│   │   test0
│   │	train1
│   │	test1
│   │	...
│   │	train9
│   │	test9
│  
│   
└───utils.py
│   │   centralization() # 中心化函数
│   │   PCA() # 主成分降维
│   │	reconstrcut() # 用特征向量重构图片
│   │	project() # 图片在特征空间上的投影
│   │   load_images() # 加载图片，因为文件名格式的特殊性，顺便加载了图片的类别
│   
│   
│   
└───EigenFace.py # 第一种实现
│   │   predict() # 预测图片类别
│   │   accuracy() # 预测正确率
│   
│ 
└───EigenFace2.py # 第二种实现

```

## EigenFace的第一种实现

**每一类图片都生成一个特征脸与平均脸**，预测新图片的类别时，先减去平均脸，再使用每类特征脸重构，重构误差最小的类别，即此图片的类别。运行程序`python EigenFace.py`，预测正确率会直接输出到控制台中，预测结果会保存在生成的predict_result文件中。
大多数测试集上表现的还可以，但有两个数据集效果奇差。

## EigenFace的另一种实现

**将所有图片放在一起，只生成一组特征脸与平均脸**。同时，所有图片表示成一个K维向量（K是特征向量的个数）。预测新图片类别时，先减平均脸并降维，再计算测试图片与训练图片的欧式距离，距离最小的几张图片决定它的类别（即KNN算法）。


## 总结与对比
1. 比较识别精度，两种实现方式基本没有差别，甚至表现不好的测试集都一致。
2. 观察表现不好的数据集预测结果（生成的predict_result文件）：第二种算法（全部的图片生成一组特征向量）与第一种相比，当KNN的K =取1时，同一张图片的预测类别基本一致，甚至预测错误的类别也一致。但是，增大K为5，同一张图片的预测类别就不太一样了。有部分本来预测错误的现在可以预测正确了。猜想是，K 取的小，样本中潜在的噪声点也可能使模型发生错误。但个人认为，第一种算法为每一类都生成特征向量的做法，应该对噪声点不敏感。有待进一步验证。
3. 第二种算法（全部的图片生成一组特征向量）的运行速度明显慢于第一种（每一类都生成特征向量）。猜想原因：大矩阵计算特征值消耗更多的时间。而更重要的是，计算测试图片和所有训练图片的欧式距离是非常耗时的。如果预测精度差不多的情况下，选择第二种算法更合适。有待进一步验证。
4. 对于第二种实现，增大KNN算法的K，对预测精度的影响似乎不大，有的数据集上预测精度上升了，但也有下降的数据集。
