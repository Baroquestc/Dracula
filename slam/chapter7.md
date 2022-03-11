## Chapter 7 视觉里程计

### 7.1 特征点法

#### 7.1.1 ORB 特征

ORB 特征：

- Oriented FAST（关键点）：FAST 关键点
- BRIEF（描述子：Binary Robust Independent Elementary Features）

### 7.5 三角测量

在单目 SLAM 中，仅通过单张图像无法获得像素的深度信息，我们需要通过三角测量（Triangulation）（或三角化）的方法来估计地图点的深度。

三角测量是指，通过在两处观察同一个点的夹角，确定该点的距离。



### 7.9 3D-3D: ICP

ICP 求解：

- 线性代数的求解（主要是 SVD）
- 非线性优化方式的求解（类似于 Bundle Adjustment）

