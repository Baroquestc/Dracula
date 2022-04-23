# SMPLify-x

> link:[https://zhuanlan.zhihu.com/p/137235901](https://zhuanlan.zhihu.com/p/137235901)
code:[https://github.com/vchoutas/smplify-x](https://github.com/vchoutas/smplify-x)
> 

## 任务

通过输入一张RGB的人体图像和由openpose检测出的人体关键点来重建人体的三维模型，其中包含肢体的动作、人脸的表情和手部动作，如图所示：

![SMPLify-x.jpg](https://dracula-images.oss-cn-beijing.aliyuncs.com/draculadraculav2-ab3a993e4d09386612d22a876f53653e_720w.jpg)

## SMPL简介

SMPL-x是德国马普所基于过去的smpl模型升级而来，所以这里先介绍SMPL模型。SMPL模型可以使用一个85维的向量来表示，其中包含人体关键点信息72维，体型信息10维，摄像机位置参数3维。

### 人体关键点信息

SMPL中包含共24个人体关键点，每个关键点信息由三个数字组成，所以总共72维，用表示。其中每个点的三个数字表示的时肢体旋转的三个角度（yaw，roll，pitch）而不是坐标，如下图所示：

![SMPLify-x.png](https://dracula-images.oss-cn-beijing.aliyuncs.com/draculav2-f6b5d942e038cf6247dda1f38a8fd341_720w.png)

![SMPLify-x.png](https://dracula-images.oss-cn-beijing.aliyuncs.com/draculav2-d777221bd79ca0b31d5c0b76afa4059a_720w.png)

http://blog.sciencenet.cn/blog-465130-1177111.html

通过肢体的旋转可以表示任意动作，但是不一定都合理。同时提供了SMPLify方法用于从一张RGB的人体图像和由openpose检测出的人体关键点来建立有相同体型和动作的SMPL模型。

### 体型信息

SMPL预设了很多种体型，并通过10个数字的组合去选择不同的体型来拟合图像的任务，用表示，如下图所示：

[https://vdn1.vzuu.com/SD/9c98165e-239b-11eb-b2b9-b6d50edeb516.mp4?disable_local_cache=1&auth_key=1634479210-0-0-74e9cd9725011ed25c01c0b0022d7e4f&f=mp4&bu=pico&expiration=1634479210&v=hw](https://vdn1.vzuu.com/SD/9c98165e-239b-11eb-b2b9-b6d50edeb516.mp4?disable_local_cache=1&auth_key=1634479210-0-0-74e9cd9725011ed25c01c0b0022d7e4f&f=mp4&bu=pico&expiration=1634479210&v=hw)

[https://vdn1.vzuu.com/SD/7732567e-2362-11eb-a9ef-fe27a5b7ef44.mp4?disable_local_cache=1&auth_key=1634479261-0-0-af624c00cd303bc06597f4790cabf991&f=mp4&bu=pico&expiration=1634479261&v=hw](https://vdn1.vzuu.com/SD/7732567e-2362-11eb-a9ef-fe27a5b7ef44.mp4?disable_local_cache=1&auth_key=1634479261-0-0-af624c00cd303bc06597f4790cabf991&f=mp4&bu=pico&expiration=1634479261&v=hw)

https://blog.csdn.net/chenguowen21/article/details/82793994

## SMPLify-x简介

SMPLify-x的大致流程如下：

1. 使用openpose检测一张RGB图像上得人体骨骼关键点，手部关键点和人脸关键点
2. 使用smpl-x模型去拟合第1步检测出的关键点数据

### smplify-x比smplify的提升

- 使用变分自编码器（VAE）在MoCap数据集中学习了一个新的，表现更好的动作先验。因为从2d到3d的映射是非常模糊暧昧的，所以这种先验非常重要
- 定义了一个新的互穿惩罚项（interpenetration penalty term），比smplify中的近似方法更准确有效
- 训练了一个性别检测器，判断该用哪套模型（模型分为男性女性和中性）
- 使用pytorch实现训练直接回归smpl-x参数，比之前的smplify中使用chumpy快8倍

### 评估数据集

建立了一个评估数据集，包含各种人体和动作，使用扫描系统捕获人体形状，再将smpl-x拟合到扫描中

## SMPL-x讲解

smpl-x表面总计=10475个角点（vertices），由=54个关键点控制。若由数学公式来表达，smpl-x模型可以表达为

![Untitled](https://dracula-images.oss-cn-beijing.aliyuncs.com/draculaUntitled.png)

其中$\theta$表示动作参数每个参数表示相对于父节点的旋转关系（yaw，roll，pitch）,$\theta\in{R^{3(K+1)}}$，$K+1$是因为除了上面提到的K个关键点，还有一个用于控制整个身体的旋转。

其中的$\theta$可以分为以下三类：$\theta_f$是脸部的脸部关键点（jaw joint），$\theta_h$是手指的关节点，$\theta_b$是肢体上的关键点。另外公式中的参数$\beta$用来表示人的体型，参数用来表示面部表情。整个操作的意义就是输入以上三种参数，最终输出人体3d模型表面上每一个顶点的坐标，即,每个顶点都是一个三维坐标，所以是。

接下来解释中每个部分的意义：

其中的$T_P$表示在平均模型的基础上，分别经过**体型、动作和表情参数**修正之后的人体模型。$B_S(\beta;S)$、和分别代表了体型、动作和表情参数计算出的在平均模型上的修正数据。其中的、和分别是模型表面顶点位移的正交分量。

注意，这里的其实就是预设好的人体模型，表示依据体型和表情参数去修改人体模型，将模型的外观依照参数去形变成需要的样子，再加上并不是说在此时就要给模型加上参数所示的动作了，而是在预设的动作（T pose）上依据将要做出的动作再次改变形体。

例如一个比较胖的人，腹部有赘肉，那么在下腰这个动作时腹部的赘肉就会层叠起来，这时就是将这个将模型的体型修正得比较胖，而下腰时腹部赘肉的层叠则由来进行修正。以上这些操作都是在改变模型的体型，均没有改变模型的动作。

表示从模型的表面顶点中获取每个关键点的坐标。

最终就表示将修正好的形体在关键点处进行旋转再辅以形变权重，最终得到我们需要的带有动作的人体模型出来。

SMPL-x的输入参数总共119个，其中75个人体关键点信息（（24+1）×3），24个手部动作信息（经过PCA降维，原本应该是90个，用表示），10个体型信息和10个表情信息。

## SMPLify-x讲解

smplify-x是smpl-x的一种实现，主要功能就是从一张RGB图像和openpose的关节点上直接得到具有当前人体体型、动作和表情的模型。主要方式是从图像上计算目标函数的最小值：

其中的、、和是手部动作，脸部动作（jaw），体型和面部表情的L2正则项。

> 原文这里的表述是：
> 

表示一个用于膝盖和肘部弯曲的简单先验，大概的意义就是人的膝盖和肘部只能向一个方向弯曲，在数据中即只能向负方向弯曲，所以当为正时会让整个公式变大，由此给予惩罚。

表示一个重投影损失。其中表示表示从模型的表面顶点中获取每个关键点的坐标，表示依据动作参数给每个关键点做出旋转，从而得到符合当前动作的每个关键点的坐标。表示按照摄像机的位置将每个关键点的三维坐标投影到二维，再减去由openpose预测出的每个关键点二维坐标。考虑到检测过程中的噪声，每个关键点对于公式的贡献由检测置信分数加权得到，对于每个关节的权重进行优化，表示一个鲁棒Geman-McClure误差方程，常用于图像匹配，用于比对openpose和预测关键位置的偏差。

身体的先验除了前面的提到的膝盖和肘部之外还有很多（毕竟很多动作都是人根本做不出来的），所以必须还要有一个全身的先验，用来惩罚那些检测出的根本做不来的动作。前人的smplify使用在MoCap数据上训练的高斯混合模型的负对数近似，有效，但是还不够强大。所以smplify-x使用变分自编码器（VAE）训练了一个全身先验VPoser。Vposer学习了一个潜码（latent code）来表示人体的动作并将其正则化为正态分布。目标函数中的同样是一个正则项，但是其中的表示的是一个函数，其中表示一个低维的动作空间，也就是上述Vposer学到的潜码。VAE的损失函数如下所示：

其中是自编码器的隐空间，表示每一个关键点的旋转矩阵，这也是VAE网络的输入，是网络的输出。表示KL散度，用于衡量训练之后的分布与正态分布之间的度量。遵循VAE公式。和的作用是保证训练结果的正态分布并保证由潜码重建之后的效果与之前的相符。和保证VAE对于旋转矩阵进行编码。防止过拟合。

最后一个用来表示一个互穿惩罚。因为在实际的拟合过程中，通常会发生身上的几个部位互相穿透（俗称穿模）的情况，smplify是基于图元的碰撞模型来进行惩罚，这种方法比较有效，但只是对于人体的基础近似。而smplify-x还要对手部和面部进行预测，所以更需要更加精确的碰撞模型。这里借鉴了《Motion capture of hands in action using discriminative salient points》和《Capturing hands in action usintg discriminative salient points and physcis simulation》的细节的基于碰撞的表面模型。首先使用BVH检测一系列碰撞三角形（模型的表面都是一个个顶点组成的三角形），然后由三角形及其法线计算局部圆锥3d距离场。接着由距离场中的位置计算入侵深度并对侵入进行惩罚。对于两个碰撞的三角形和，它们的侵入是互相的，在的距离场中，上的顶点是侵入者，而则是接受者，其应该由进行惩罚，反之亦然。所以的公式如下：

## 性别分类器

为了拟合得更加准确，需要先知道照片上的人的性别，这样才能更好的选择预设模型。smplify-x训练了一个分类器，将包含全身的图像和openpose关键点输入，使用Resnet18进行训练，最终输出性别概率。若最终输出的性别概率均低于阈值，则直接使用中性模型。

## 优化过程

之前的smplify采用chumpy和OpenDR进行优化，但是优化速度非常慢（而且在python3上配环境很麻烦），所以这一次的smplify-x采用pytorch和L-BFGS进行有强Wolfe条件的线搜索。在优化中采用多级优化。首先假设我们已知相机焦距的确切值或者近似值，估计相机的平移和身体的方位（与smplify相同）。然后固定相机参数，优化体型参数和动作参数。这里采用退火方案来处理局部最优解（表现在上），先提高肢体动作部分的值来计算肢体的动作，再提升手部的值计算手部和胳膊的参数，接着提升双手和脸部。在优化过程中肢体、手部、脸部表情的都是先高后低，但是会逐渐增大，因为手部的变大之后会产生更多的穿透。

> 参考资料
> 
> 
> [zhuanlan.zhihu.com/p/10](https://zhuanlan.zhihu.com/p/105216364)
> 
> [blog.sciencenet.cn/blog](https://link.zhihu.com/?target=http%3A//blog.sciencenet.cn/blog-465130-1177111.html)
> 
> [blog.csdn.net/chenguowe](https://link.zhihu.com/?target=https%3A//blog.csdn.net/chenguowen21/article/details/82793994)