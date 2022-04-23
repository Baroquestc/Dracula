# FaceReconstruction

## 1 什么是3D人脸重建？

       **人脸3D重建：从一张或多张2D图像中重建出人脸的3D模型。**  
       **3D人脸模型：**  
       我们可以用这样一个表达式来建模3D人脸模型：  
M = ( S , T ) M = (S,T) M\=(S,T)  
       其中， S S S表示人脸3D坐标形状向量（shape-vector）：  
S = ( x 1 , y 1 , z 1 , x 2 , y 2 , z 2 , ⋯ &ThinSpace; , x n , y n , z n ) T S = (x\_1,y\_1,z\_1,x\_2,y\_2,z\_2,\\cdots,x\_n,y\_n,z\_n)^T S\=(x1,y1,z1,x2,y2,z2,⋯,xn,yn,zn)T  
        T T T表示对应点的文理信息向量（texture-vector）：  
T = ( R 1 , G 1 , B 1 , R 2 , G 2 , B 2 , ⋯ &ThinSpace; , R n , G n , B n ) T T = (R\_1,G\_1,B\_1,R\_2,G\_2,B\_2,\\cdots,R\_n,G\_n,B\_n)^T T\=(R1,G1,B1,R2,G2,B2,⋯,Rn,Gn,Bn)T  
       2D的人脸图片可以看作是3D人脸在2D平面上的一个投影， I I I 代表 M M M的2D投影， I ( u , v ) I(u,v) I(u,v)代表像素 ( u , v ) (u,v) (u,v)处的纹理值，所以3D人脸重建就是从2D图片中计算出 M M M的估计：  
M ^ = ( S ^ , T ^ ) \\hat M = (\\hat S,\\hat T) M^\=(S^,T^)  
       用图像表示更为直观：

![这里写图片描述](https://img-blog.csdn.net/20180911112032827?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 2 重建方法分类

       在过去20年中，研究者们在3D人脸重建方面贡献甚多，2D人脸图像到3D人脸重建方法也是多种多样，硬要为其做个分类，大致如下图（当然可能不太全面）：


![这里写图片描述](https://img-blog.csdn.net/20180911113350359?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       传统3D人脸重建方法，大多是立足于图像信息，如基于**图像亮度、边缘信息、线性透视、颜色、相对高度、视差**等等一种或多种信息建模技术进行3D人脸重建。这方面的技术论文，无论国内外都相当多，也较杂乱，一时间个人也不好具体统计，总之其中也是有很多不错的思想和方法的，当然这也不是本文重点内容。  
       基于模型的3D人脸重建方法，是目前较为流行的3D人脸重建方法；3D模型主要用三角网格或点云来表示，现下流行的模型有**通用人脸模型（CANDIDE-3）**和**三维变形模型（3DMM）及其变种**模型，基于它们的3D人脸重建算法既有传统算法也有深度学习算法。  
       端到端3D人脸重建方法，是近年新起的方法；它们绕开了人脸模型，设计自己的3D人脸表示方法，采用CNN结构进行直接回归，端到端地重建3D人脸。

## 3 通用模型3D人脸重建

       得到人脸的通用模型，通常有三种方法：第一种方法是采用三维扫描仪获取数据，此方法采集精度高但设备价格昂贵；第二种方法是采用计算机图形技术创建人脸；第三种是利用一些商业的建模软件生成人脸通用模型，目前在市场上比较著名的人头模型生成商业软件有FaceGen Modeller，3D Max 和Poser7.0 等等。  
       在众多通用人脸模型中，**CANDIDE-3模型**是目前被学术界广泛使用的一种通用模型，其符合MPEG-4标准中对人脸的定义。  
       CANDIDE-3模型总共有**113个顶点**，**168个面**组成，可以通过对这些点和面的操作调节形成特定的人脸，下图为CANDIDE-3的模型展示图：


![这里写图片描述](https://img-blog.csdn.net/20180911132601462?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       通用模型重建法的本质是对通用模型进行修改使得其特征与所需要恢复的输入图像相配，一般包括**整体性调整**和**局部性调整**两个方面。整体性调整主要是针对模型的轮廓，通过一定的方法如特征点对应使得通用模型的整体布局（如眼耳鼻眉）和输入图片的五官布局尽量一致；局部性调整指的是针对局部细节尤其是人脸五官的微调，让局部细节更为精确。在进行完这两项调整之后，再辅助以**基于顶点的插值运算**就可以重建人脸。通用模型法的优点是**计算量较小**，但其显著缺陷就是因**顶点数目过少**导致对人脸轮廓的模拟和**面部细节刻画不够细腻**，故只能适用于精度要求不高的场合。

## 4 3D变形模型3D人脸重建

       形变模型（Morphable Model）这一名词来源于计算机图形学中一个名叫Morphing技术的图像生成算法。Morphing 技术主要思想：如果两幅图像中存在一定的对应关系，那么就可以利用这个对应关系生成具一副有平滑过渡效果的图像。

## 4.1 初版3DMM

       文献：《A Morphable Model For The Synthesis Of 3D Faces》  
       在这个思想的引导下，在1999 年，瑞士巴塞尔大学的科学家Blanz 和Vetter 提出了一种十分具有创新性的方法：**三维形变模型(3DMM)** 。三维形变模型建立在**三维人脸数据库**的基础上，以**人脸形状和人脸纹理**统计为约束，同时考虑到了人脸的姿态和光照因素的影响，因而生成的三维人脸模型精度高。  
       3DMM模型数据库人脸数据对象的线性组合，在上面3D人脸表示基础上，假设我们建立3D变形的人脸模型由m个人脸模型组成，其中每一个人脸模型都包含相应的 S i S\_i Si， T i T\_i Ti两种向量，这样在表示新的3D人脸模型时，我们就可以用以下的方式：  
S n e w M o d e l = S ‾ + ∑ i = 1 m − 1 α i s i S\_{newModel} =\\overline{S} + \\sum \_{i=1}^{m-1} \\alpha\_is\_i SnewModel\=S+i\=1∑m−1αisi  
T n e w M o d e l = T ‾ + ∑ i = 1 m − 1 β i t i T\_{newModel} = \\overline{T} + \\sum \_{i=1}^{m-1} \\beta\_it\_i TnewModel\=T+i\=1∑m−1βiti  
       其中 S ‾ \\overline S S表示平均脸部形状模型， s i s\_i si表示shape的PCA部分， α i \\alpha\_i αi表示对应系数；文理模型同理。  
       像这样，一张新的人脸模型就可以由已有的脸部模型线性组合。也就是说，我们可以通过改变系数，在已有人脸基础上生成不同人脸。


![这里写图片描述](https://img-blog.csdn.net/20180912112312746?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 4.2 表情3DMM

       初版3DMM虽然解决了人脸变形模型的表达，但其在人脸表情表达上面明显不足，在2014年时，FacewareHouse这篇论文提出并公开了一个人脸表情数据库，使得3DMM有了更强的表现力。从而人脸模型的线性表示可以扩充为：  
S n e w M o d e l = S ‾ + ∑ i = 1 m − 1 α i s i + ∑ i = 1 n − 1 β i e i S\_{newModel} =\\overline{S} + \\sum \_{i=1}^{m-1} \\alpha\_is\_i + \\sum \_{i=1}^{n-1}\\beta\_i e\_i SnewModel\=S+i\=1∑m−1αisi+i\=1∑n−1βiei


![这里写图片描述](https://img-blog.csdn.net/20180912125111904?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 4.3 人脸重建

       参考文献：[https://blog.csdn.net/likewind1993/article/details/81455882](https://blog.csdn.net/likewind1993/article/details/81455882)  
       从上面可以看出，人脸重建问题就转向了求 α \\alpha α， β \\beta β问题，为了简便，这里就只讨论Shape重建，文理重建同理。

       在有了形变模型之后，对于一张给定的人脸照片，我们需要将模型与人脸照片进行配准，然后对模型的参数进行调整，使其与照片中的人脸差异值达到最小。简单而言，不断依据模型与输入的人脸照片进行比对，不断进行迭代，使两者之间的比对误差达到最小，这个时候，我们可以近似认为该模型即为对应输入的人脸照片的三维模型。

       如我们有单张人脸图像，以及68个人脸特征点坐标 X X X，在BFM模型中有对应的68个3D特征点 X 3 d X\_{3d} X3d，根据这些信息便可求出 α \\alpha α， β \\beta β系数，将平均脸模型与图像的脸部进行拟合，具体如下：  
X p r o j e c t i o n = s ∗ P ∗ R ∗ ( S ‾ + ∑ i = 1 m − 1 α i s i + ∑ i = 1 n − 1 β i e i ) + t 2 d X\_{projection}=s\*P\*R\*(\\overline S + \\sum \_{i=1}^{m-1} \\alpha\_is\_i + \\sum \_{i=1}^{n-1}\\beta\_i e\_i ) + t\_{2d} Xprojection\=s∗P∗R∗(S+i\=1∑m−1αisi+i\=1∑n−1βiei)+t2d  
       这里 X p r o j e c t i o n X\_{projection} Xprojection是3D模型投影到2D平面的点， s s s是缩放系数， P = \[ \[ 1 , 0 , 0 \] , \[ 0 , 1 , 0 \] \] P=\[\[1,0,0\],\[0,1,0\]\] P\=\[\[1,0,0\],\[0,1,0\]\]为正交投影矩阵， R R R为3x3的旋转矩阵， t 2 d t\_{2d} t2d为位移矩阵。  
       所以，3D求解过程又转化为求解满足以下能量方程的系数：  
a r g m i n ( ∣ ∣ X p r o j e c t i o n − X ∣ ∣ 2 + λ ∑ i = 1 ( γ i σ i ) 2 ) argmin(||X\_{projection} - X||^2 + \\lambda \\sum \_{i=1}(\\frac{\\gamma\_i}{\\sigma\_i})^2) argmin(∣∣Xprojection−X∣∣2+λi\=1∑(σiγi)2)  
       其后项是正则化项，其中 γ \\gamma γ表示PCA系数， σ \\sigma σ表示对应的主成分偏差。

## 4.4 LSFM-3DMM

       参考文献：[http://36kr.com/p/5073179.html](http://36kr.com/p/5073179.html)  
       计算机处理人脸时，有时会依赖一种所谓的“3D 变形模型”（3DMM）。这一模型代表了一个平均人脸，但同时也包含了与这一平均值常见的偏差模式信息。例如，如长了一个长鼻子的脸也可能有一个很长的下巴。鉴于这种相关性，计算机可以在不将有关你脸部全部表征信息都存储下来的情况下，只列出几百个数字描述你的脸与平均人脸的偏差，就能够生成专属于你的面部图像了。不仅如此，这些偏差还包括大致的年龄、性别和脸部长度等参数。

       但是，这样也有一个问题。因为世界上的人脸千变万化，要将所有人脸与平均人脸的偏差都存储下来，3DMM 需要集成许多面部的信息。目前为止所采用的方法是扫描大量人脸，然后人工仔细标记所有的特征。也因此，目前最好的模型也只是基于几百张人脸——大部分还都是白人，而且模型在模仿不同年龄和种族人脸方面的能力十分有限。

       **1 LSFM**  
       论文：[《Large Scale 3D Morphable Models》](https://link.springer.com/article/10.1007/s11263-017-1009-7)  
       我们提出了一个大规模的人脸模型（LSFM），这是一个3维形变模型（3DMM），从9663个独特的脸部标志中自动地搭建。研究者介绍，据他们了解，LSFM 是迄今为止被构建出来的最大规模的形变模型，包含了从一个巨大的人口变量中提取的统计信息。为了搭建出这样一个模型，研究者创造了一个全新而且全自动且稳定的形变模型搭建管道，通过对目前最好的密集（dense）通信技术进行评估来获得信息。  
       训练 LSFM 的数据集包括关于每个主题的丰富的人口统计信息，不仅可以构建全球3DMM模型，还可以构建针对特定年龄，性别或族裔群体的模型。研究者利用所提出的模型，从3D形状单独执行年龄分类，并重建低维模型空间中的噪声样本外数据。此外，他们还对构建的3DMM模型进行系统分析，展示其质量和描述能力。  
       研究提出的深度定性和定量评估表明，他们所提出的3DMM取得了最先进的结果，大大优于现有模型。最后，为了造福研究社区，他们公开了所提出的自动3DMM建造管道的源代码，以及构建的全球3DMM和根据年龄，性别和种族量身定制的各种定制模型。


![这里写图片描述](https://img-blog.csdn.net/20180912135438441?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       （1）基于综合呈现视图进行自动标记。这些视图注册了像素级的形状信息，因此可将 2D 标记（landmark）可信地投影回 3D 表面；  
       （2）在自动标记的引导下，3D 模型不断迭代变形，以精确匹配数据集的每个 3D 面部网格；  
       （3）构建初步的全局 PCA；  
       （4）自动删去错误的对应；  
       （5）由剩余的干净数据构建 LSFM 模型。

       **2 LSFM应用**  
       论文：[《Face Normals “in-the-wild” using Fully Convolutional Networks》](https://ibug.doc.ic.ac.uk/media/uploads/documents/normal_estimation__cvpr_2017_-4.pdf)  
       本研究中，提出了一种数据驱动型的方法，解决如何从一张单独的浓缩照片中预测整个一般平面，特别聚焦于人脸。论文创造了一种新的方法，探索将可用的人脸数据集用于数据库建设的可行性。研究者特意设计了一个深度卷积神经网络来评估人脸“in-the-wild”状态下的正常表面。还训练了一个全卷积的网络，能够精确地从多样化的表情的图片中还原正常表情。我们比较最先进的 Shape-from-Shading 和 3D 重建技术，结果表明我们提出的网络可以大幅度恢复更正确和逼真的人脸。而且，相比其他现有的面部特异性表面恢复方法， 由于我们网络的完全卷积性质，我们不需要解决明确的对齐问题。


![这里写图片描述](https://img-blog.csdn.net/20180912140414482?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       这些都是从由 300W 人脸数据集 “in-the-wild” 生成的。上图展示了作者提出的网络能广泛适用于多种不同人脸和表情。最左边是 300W 数据集的原始图像。中间一列是 3D形状重建，右边一列是将图像中采样到的纹理转换为形状。

## 4.5 Nonlinear-3DMM

       论文：[《Nonlinear 3D Face Morphable Model》](https://github.com/tranluan/Nonlinear_Face_3DMM)  
       论文：《[On Learning 3D Face Morphable Model from In-the-wild Images](https://arxiv.org/abs/1808.09560v1)》  
       传统的3DMM是从2D人脸图像的3D人脸扫描中学习的，并由两组PCA基函数表示。由于训练数据的类型和数量，以及线性基础，3DMM的表示能力是有限的。所以提出了Nonlinear-3DMM，更好地表达人脸信息。


![这里写图片描述](https://img-blog.csdn.net/20180912144608608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       本文提出的Nonlinear-3DMM是通过DNNs来学习Shape和Texture，不需要3D扫描，能够更好地重建人脸。


![这里写图片描述](https://img-blog.csdn.net/20180912145331510?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 5 端到端3D人脸重建

       端到端3D人脸重建方法，是近年新起的方法；它们绕开像3DMM的人脸模型，设计自己的3D人脸表示方法，采用CNN结构进行直接回归，端到端地重建3D人脸，现下主要代表有VRNet和PRNet。

## 5.1 VRNet

       [《Large Pose 3D Face Reconstruction from a Single Image via Direct Volumetric》](https://github.com/AaronJackson/vrn)  
       **1 主要思想：**  
       该论文提出了自己的3D人脸表示方法，即用一个**192_192_200的Volumetric**来表示，是什么意思呢？  
       我的理解是，将人脸看成是从耳后平面到鼻尖平面的200个横切片，每张切片就是等高点，如下图：


![这里写图片描述](https://img-blog.csdn.net/20180911134841174?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       **2 CNN结构：**  
       这样一来，我们CNN就可以直接回归出这个Volumetric，而不是是直接预测顶点坐标，其结构如下：


![这里写图片描述](https://img-blog.csdn.net/20180911140014818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       如上图，基础CNN结构采用沙漏网络，论文提出了三种方案，（a）表示直接从图片重建，（b）表示加入了人脸特征点作为引导，（c）表示多任务（重建+人脸特征点预测）结构；实验证明效果最好的是第二种方法。

       **3 重建效果：**


![这里写图片描述](https://img-blog.csdn.net/20180911140617303?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       该方法优点是，设计了自己的3D人脸表示方法，结构简单粗暴，收到不俗的效果，论文代码链接中有官方在线demo，读者可以体验。

       **4 总结：**  
       该方法缺点是，由CNN预测出来的3D人脸的顶点是不固定的，也就是我们还需要进行一步对齐，将一个固定顶点的模板对齐到CNN预测出来的3D人脸；重建分辨率不易扩展，整体计算量较大。

## 5.2 PRNet

       [《Joint 3D Face Reconstruction and Dense Alignment with Position Map Regression Network》](https://github.com/YadiraF/PRNet)  
        本文提出了一种能够同时完成3D人脸重建和稠密人脸对齐的端对端的方法–**[位置映射回归网络（PRN）](https://blog.csdn.net/u011681952/article/details/82383518)**。

       **1 主要思想：**  
       设计**UV位置映射图**，它是一种记录所有面部点云3D坐标的2D图像，在每个UV多边形中都保留了语义信息；然后设计一种**加权损失**训练一个简单的**CNN（编码-解码）网络**，从单张2D人脸图像中回到UV位置映射。  
        我们任务目的是从单2D人脸图像回归出人脸3D几何结构信息以及它们之间的稠密关系。所有我们需要适合的表示方法，并用一个网络来直接预测；一个简单且普遍的是用一个**1D向量**来表示，即将3D点信息用一个向量来表示，然后用网络预测；然而，这种方法**丢失了空间信息**。  
        相关研究中也提到了类似于3DMM等模型表示，但这些方法太过依赖3DMM模型，并且流程复杂；最近的**VRN**用Volumetric来表示，成功摆脱了上述问题，但是其网络需要输出192x192x200的一个Volume，计算量相当大，重建分辨率将会受到限制。  
        针对这些问题，本文设计了UV position map来表示，如下图：


![这里写图片描述](https://img-blog.csdn.net/20180911142611600?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

        左图是输入图像的3D图和3D点云ground-truth；右边第1行，分别是2D图像，UV文理映射图，相应的UV位置映射图；右边第2行，分别是UV位置映射图的x，y，z通道。  
        然而开源数据集，如300W-LP，的ground-truth是没有用UV表示的，而3DMM，所有我们是基于3DMM创建的UV标签。3DMM又是依据BFM（Basel Face Model）【20】，所有我们UV还得与BFM建立联系，[文献【35】](https://github.com/anilbas/3DMMasSTN#uv-texture-space-embedding-for-basel-face-model)中提到解决办法（具体细节，待考证）。最终我们选择256最为UV尺寸，约50K个点来表示Face mesh。

       **2 CNN结构：**  
        有了UV表示方法，我们就可以直接用CNN网络回归UV参数，如下图：


![这里写图片描述](https://img-blog.csdn.net/20180911142856366?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

        采用encoder-decoder结构，256x256x3的输入人脸图像，输出256x256x3的位置映射图；编码结构采用10个残差块级联构成，解码结构有17层反卷积层构成，激活层采用ReLU，最后输出层采用Sigmoid激活函数。

       **3 Loss Function：**  
        为了强调重点，优化性能，本文还设计了weight mask形式的Loss function：


![这里写图片描述](https://img-blog.csdn.net/20180911142916761?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

        其意义在于，不同位置或区域，计算error时权重不一样，看重或者特殊区域权重给高点，网络就会更关注这块。总共分为4中不同等级，如下图：


![这里写图片描述](https://img-blog.csdn.net/20180911142929424?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

        4等级：特征点（68点），眼睛、鼻子、嘴巴，其他脸部，脸以外的部分；他们的权重分别比：16:4:3:0

       **4 重建效果：**


![这里写图片描述](https://img-blog.csdn.net/20180911143249693?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       **5 总结：**  
       该方法结构简单，便于实现，也便于进一步优化，并取得不俗效果；但也存在问题，生成的mesh存在明显条纹：


![这里写图片描述](https://img-blog.csdn.net/20180911143904460?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

       尤其是鼻子部分，个人认为这是由于UV图像映射到3D时必然存在的，但或许可以通过平滑或插值来解决这个问题，如平滑后：

![这里写图片描述](https://img-blog.csdn.net/20180911144236728?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE2ODE5NTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 5.3 2DASL（4.15更新）

       2DASL是19年发表的论文，本人还没细看这篇大作，据说效果比前文中的方法提升不少；先占个位置，等有空闲再来更新细节  
       论文：《[Joint 3D Face Reconstruction and Dense Face Alignment from A Single Image with 2D-Assisted Self-Supervised Learning](https://arxiv.org/pdf/1903.09359.pdf)》  
       来源：电子科技大学、新加坡国立大学、国防科技大学、中科大、中科院自动化所  
       源码：[https://github.com/XgTu/2DASL](https://github.com/XgTu/2DASL)

       **论文Pipeline：**


![在这里插入图片描述](https://dracula-images.oss-cn-beijing.aliyuncs.com/dracula20190415094828183.png)

       **论文效果：**


![在这里插入图片描述](https://dracula-images.oss-cn-beijing.aliyuncs.com/dracula20190415095025984.png)

## 6 网格优化

Laplacian Mesh Optimization：[https://blog.csdn.net/linmingan/article/details/79742027](https://blog.csdn.net/linmingan/article/details/79742027)  
拉普拉斯网格优化：[https://blog.csdn.net/hjimce/article/details/46505863](https://blog.csdn.net/hjimce/article/details/46505863)

## 7 人脸数据库

官网：[http://www.face-rec.org/databases/](http://www.face-rec.org/databases/)  
博客：[https://blog.csdn.net/holybin/article/details/25735093](https://blog.csdn.net/holybin/article/details/25735093)