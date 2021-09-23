# OpenCV基本操作

图片由h \* w \* c个像素组成（一般分为R/G/B三个通道时，每个通道图片由h \* w个像素组成），像素范围在0-255(即黑-白)，故图片的numpy数组格式为uint8足以。

## 1 数据读取

opencv读取的格式是BGR

>-   cv2.IMREAD\_COLOR：彩色图像
>-   cv2.IMREAD\_GRAYSCALE：灰度图像
>

【注】matplotlib inline 在jupyter展示中,图绘制完了直接进行展示,不用plt.show()显示

```python
import cv2 
import matplotlib.pyplot as plt
import numpy as np 
%matplotlib inline 

img = cv2.imread('cat.jpg')
```

### 图像的显示

```python
cv2.imshow('image',img) 
cv2.waitKey(0) 
cv2.destroyAllWindows()
```

规范一下，写个小函数，定义图像名 并 传入已读取的图像，即可显示

```python
def cv_show(img_name,img):
    cv2.imshow('image',img)
    cv2.waitKey(0)
    cv2.destroyAllWindows()
```

### 读取格式的转换

读取图片的同时转换为灰度图像  
img.shape 图像的形状

```python
img_gray = cv2.imread('cat.jpg', cv2.IMREAD_GRAYSCALE)
print(img_gray, img_gray.shape)
cv_show('cat_gray',img_gray)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729033050463.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 图像保存

imwrite需要两个参数：(路径名)图片名, 图

```python
cv2.imwrite('cat_gray.jpg',img_gray)
```

### 基础属性

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200729034225834.png)

### 截取部分图像数据

感兴趣区域（ROI）的截取 \[x:x+w,y:y+h\]

```python
img = cv2.imread('cat.jpg')
img_crop = img[100:200,50:200]
cv2.imshow('cat_crop',img_crop)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730013031909.png)

## 颜色通道提取、组合

-   split 分离颜色通道
-   merge 组合颜色通道(注 两个括号)

```python
import cv2
b,g,r = cv2.split(img)
b[100:200,50:200] = 0
img_new = cv2.merge((b,g,r))
cv2.imshow('img_new',img_new)
cv2.waitKey(0)
```

比如将蓝色通道都置0，绿 红通道 这块区域的值没变，则组合成黄色  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730014025629.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
单独保留某个通道，其他通道置0  
![](https://img-blog.csdnimg.cn/20200730015451792.png)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730015341115.png)

## 边界填充

扩充图像边界 copyMakeBorder，需要6个参数：图+上+下+左+右(填充的像素大小)+填充方式  
填充方式如下：

-   BORDER\_REPLICATE：复制法，也就是复制最边缘像素。
-   BORDER\_REFLECT：反射法，对感兴趣的图像中的像素在两边进行复制例如：fedcba|abcdefgh|hgfedcb
-   BORDER\_REFLECT\_101：反射法，也就是以最边缘像素为轴(没有a)，对称，gfedcb|abcdefgh|gfedcba
-   BORDER\_WRAP：外包装法cdefgh|abcdefgh|abcdefg
-   BORDER\_CONSTANT：常量法，常数值填充。

```python
import cv2
import matplotlib.pyplot as plt
img = cv2.imread('cat.jpg')
top_size,bottom_size,left_size,right_size = (50,50,50,50)
img_replace = cv2.copyMakeBorder(img,top_size,bottom_size,left_size,right_size,cv2.BORDER_REPLICATE)
img_reflect = cv2.copyMakeBorder(img,top_size,bottom_size,left_size,right_size,cv2.BORDER_REFLECT)
img_reflect101 = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size, cv2.BORDER_REFLECT_101)
img_wrap = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size, cv2.BORDER_WRAP)
img_constant = cv2.copyMakeBorder(img, top_size, bottom_size, left_size, right_size,cv2.BORDER_CONSTANT, value=0)

plt.subplot(231),plt.imshow(img,'gray'),plt.title('ORIGINAL')
plt.subplot(232),plt.imshow(img_replace,'gray'),plt.title('Replace')
plt.subplot(233), plt.imshow(img_reflect, 'gray'), plt.title('REFLECT')
plt.subplot(234), plt.imshow(img_reflect101, 'gray'), plt.title('REFLECT_101')
plt.subplot(235), plt.imshow(img_wrap, 'gray'), plt.title('WRAP')
plt.subplot(236), plt.imshow(img_constant, 'gray'), plt.title('CONSTANT')
plt.savefig('cat_fill.jpg')
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730023926497.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 数值计算

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730025023112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 图像融合

-   cv2.resize(img, (新img的宽, 高)) 融合两张图的前提是尺寸一致

```
import cv2
img_cat = cv2.imread('cat.jpg')  
img_dog = cv2.imread('dog.jpg')

img_dog = cv2.resize(img_dog,(img_cat.shape[1],img_cat.shape[0]))
cat_dog = img_cat+img_dog
cv2.imshow('cat_dog',cat_dog)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730031815815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

```
import matplotlib.pyplot as plt

big_cat = cv2.resize(img_cat,(0,0),fx=4,fy=4)
long_cat = cv2.resize(img_cat,(0,0),fx=3,fy=1)
plt.subplot(121),plt.imshow(big_cat),plt.title('big_cat')
plt.subplot(122),plt.imshow(long_cat),plt.title('long_cat')
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730031555344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

-   cv2.addWeighted 就相当于α \* X1 + β \* X2 + b，α=0.4，β=0.6，分别是两张图片的权重，以这样的形式融合

```python
res = cv2.addWeighted(img_cat,0.4,img_dog,0.6,0)
cv_show('res',res)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200730030241882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

-   cv2.VideoCapture可以捕获摄像头，用数字来控制不同的设备，例如0,1。
-   如果是视频文件，直接指定好路径即可。

```
import cv2
vc = cv2.VideoCapture('test.mp4')

if vc.isOpened():
    opened, frame = vc.read()  
    cv2.imshow('frame',frame)
else:
    open = False
while open:
    ret, frame = vc.read()
    if frame is None:
        break
    if ret == True:
        gray = cv2.cvtColor(frame,  cv2.COLOR_BGR2GRAY)
        cv2.imshow('result', gray)
        if cv2.waitKey(100) & 0xFF == 27:
            break
vc.release()
cv2.destroyAllWindows()
```

-   cv2.waitKey(100) 隔多少毫秒显示下一张图片，设置稍大点，符合我们看视频的一个速度。太大就像看视频卡顿的感觉；太小就像几倍速播放，太快了。
-   0xFF == 27 指定退出键退出
-   0xFF == ord(‘q’) 指定q键退出

## 图像阈值

ret, dst = cv2.threshold(src, thresh, maxval, type)

-   src： 输入图，只能输入单通道图像，通常来说为**灰度图**
-   thresh：一般取127和255
-   maxval： 当像素值超过了阈值（或者小于阈值，根据type来决定），所赋予的值
-   type：二值化操作的类型，包含以下5种类型：
    -   cv2.THRESH\_BINARY 超过阈值部分取maxval（最大值），否则取0
    -   cv2.THRESH\_BINARY\_INV THRESH\_BINARY的反转
    -   cv2.THRESH\_TRUNC 大于阈值部分设为阈值，否则不变
    -   cv2.THRESH\_TOZERO 大于阈值部分不改变，否则设为0
    -   cv2.THRESH\_TOZERO\_INV THRESH\_TOZERO的反转
-   return返回值
    -   dst： 输出图
    -   thresh： 阈值

```
import cv2
import matplotlib.pyplot as plt
img = cv2.imread('cat.jpg')
img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret,img_bi = cv2.threshold(img_gray,127,255,cv2.THRESH_BINARY)
ret,img_bi_inv = cv2.threshold(img_gray,127,255,cv2.THRESH_BINARY_INV)
ret,img_tr = cv2.threshold(img_gray,127,255,cv2.THRESH_TRUNC)
ret,img_zero = cv2.threshold(img_gray,127,255,cv2.THRESH_TOZERO)
ret,img_zero_inv = cv2.threshold(img_gray,127,255,cv2.THRESH_TOZERO_INV)

titles = ['Original','Binary','Binary_INV','TRUNC','ZERO','ZERO_INV']
images = [img,img_bi,img_bi_inv,img_tr,img_zero,img_zero_inv]

for i in range(6):
    plt.subplot(2,3,i+1),plt.imshow(images[i],'gray'),plt.title(titles[i])
    plt.xticks([]),plt.yticks([])   
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731010624317.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 图像滤波（平滑）

-   cv2.blur # 均值滤波：简单的平均卷积操作
-   cv2.boxFilter # 方框滤波：基本和均值一样，可以选择归一化
-   cv2.GaussianBlur # 高斯滤波：高斯模糊的卷积核里的数值是满足高斯分布，相当于更重视中间的
-   cv2.medianBlur # 中值滤波：相当于用中值代替

```
import cv2
import numpy as np
import matplotlib.pyplot as plt
img = cv2.imread('lenaNoise.png')
blur = cv2.blur(img,(3,3))
boxFilter = cv2.boxFilter(img,-1,(3,3),normalize=False)
gussian = cv2.GaussianBlur(img,(3,3),1)
median = cv2.medianBlur(img,5)

titles = ['Original','Binary','Binary_INV','TRUNC','ZERO','ZERO_INV']
images = [img,blur,boxFilter,gussian,median]






res = np.hstack((blur,gussian,median))
cv2.imshow('median vs average', res)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731013413614.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 形态学

> 腐蚀与膨胀属于形态学操作，所谓的形态学，就是改变物体的形状，形象理解一些：**腐蚀=变瘦** **膨胀=变胖**  
> 主要是采用 cv2.erode() 和 cv2.dilate()  
> 主要针对二值化图像的白色部分  
> .
> 
> -   腐蚀：是一种消除边界点，使边界向内部收缩的过程
>     
>     -   通俗讲法：在原图的每一个小区域里取最小值，由于是二值化图像，只要有一个点为0，则都为0，来达到瘦身的目的
>     -   算法：用 3x3 的 kernel，扫描图像的每一个像素；用 kernel 与其覆盖的二值图像做 “与” 操作；若都为1，则图像的该像素为1；否则为0. 最终结果：使二值图像减小一圈.
> -   膨胀：是将与物体接触的所有背景点合并到该物体中，使边界向外部扩张的过程，可以用来填补物体中的空洞.
>     
>     -   算法：用 3x3 的 kernel，扫描图像的每一个像素；用 kernel 与其覆盖的二值图像做 “与” 操作；若都为0，则图像的该像素为0；否则为1. 最终结果：使二值图像扩大一圈  
>         .
> -   先腐蚀后膨胀的过程称为 **开运算**。用来消除小物体、在纤细点处分离物体、平滑较大物体的边界的同时并不明显改变其面积.【cv2.morphologyEx(img,cv2.MORPH\_OPEN,kernel)】
>     
> -   先膨胀后腐蚀的过程称为 **闭运算**。用来填充物体内细小空洞、连接邻近物体、平滑其边界的同时并不明显改变其面积.【cv2.morphologyEx(img,cv2.MORPH\_CLOSE,kernel)】
>     
> -   膨胀 - 腐蚀的过程称为 **梯度运算**。用来计算轮廓【cv2.morphologyEx(pie,cv2.MORPH\_GRADIENT,kernel)】
>     
> -   顶帽：原始输入 - 开运算结果 【cv2.morphologyEx(img,cv.MORPH\_TOPHAT,kernel)】
>     
> -   黑帽：闭运算 - 原始输入 【cv2.morphologyEx(img,cv.MORPH\_BLACKHAT,kernel)】
>     

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731015515153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70#pic_center)

### 腐蚀/膨胀操作

```python
import cv2
import numpy as np
img = cv2.imread('dige.png')
kernel = np.ones((3,3),np.uint8) 
erosion = cv2.erode(img,kernel,iterations = 1)  
dilate = cv2.dilate(img,kernel,iterations = 1)

res = np.hstack((img,erosion,dilate))
cv_show('dige and erode and dilate',res)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731020842799.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70#pic_center)

```
pie = cv2.imread('pie.png')
kernel = np.ones((30,30),np.uint8) 
erosion_1 = cv2.erode(pie,kernel,iterations = 1)
erosion_2 = cv2.erode(pie,kernel,iterations = 2)
erosion_3 = cv2.erode(pie,kernel,iterations = 3)

dilate_1 = cv2.dilate(pie,kernel,iterations = 1)
dilate_2 = cv2.dilate(pie,kernel,iterations = 2)
dilate_3 = cv2.dilate(pie,kernel,iterations = 3)

res_e = np.hstack((pie,erosion_1,erosion_2,erosion_3))
res_d = np.hstack((pie,dilate_1,dilate_2,dilate_3))

cv2.imwrite('pie_erode_res.jpg',res_e)
cv2.imwrite('pie_dilate_res.jpg',res_d)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020073102202379.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70#pic_center)

### 开运算&闭运算

-   开：先腐蚀，再膨胀
-   闭：先膨胀，再腐蚀

```
import cv2
import numpy as np
img = cv2.imread('dige.png')
kernel = np.ones((5,5),np.uint8)
opening = cv2.morphologyEx(img,cv2.MORPH_OPEN,kernel)
closing = cv2.morphologyEx(img,cv2.MORPH_CLOSE,kernel)

result = np.hstack((img,opening,closing))
cv2.imshow('open and close',result)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731022924708.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 梯度运算

-   梯度 = 膨胀-腐蚀  
    多出来的白边 减去 减少的白边，即计算一个轮廓出来

```python

pie = cv2.imread('pie.png')
kernel = np.ones((7,7),np.uint8) 
dilate = cv2.dilate(pie,kernel,iterations = 5)
erosion = cv2.erode(pie,kernel,iterations = 5)
gradient = cv2.morphologyEx(pie,cv2.MORPH_GRADIENT,kernel)

res = np.hstack((dilate,erosion,gradient))

cv2.imshow('res', res)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020073102372059.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 礼帽&黑帽

-   礼帽 = 原始输入 - 开运算结果 （原图 - 没刺的 = 剩下刺）
-   黑帽 = 闭运算 - 原始输入 （字和刺胖了的 - 原图 = 胖的边缘部分）

```python
img = cv2.imread('dige.png')
kernel = np.ones((7,7),np.uint8) 
tophat = cv2.morphologyEx(img, cv2.MORPH_TOPHAT, kernel)
blackhat = cv2.morphologyEx(img,cv2.MORPH_BLACKHAT,kernel)
res = np.hstack((img,tophat,blackhat))
cv2.imshow('res', res)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731025056151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 图像梯度-Sobel算子

cv2.Sobel(src, ddepth, dx, dy, ksize) 进行sobel算子计算  
参数说明：src表示当前图片，ddepth表示图片深度，这里使用cv2.CV\_64F使得结果可以是负值， dx表示x轴方向，dy表示y轴方向, ksize表示移动方框的大小

cv2.convertScalerAbs(src) 将像素点进行绝对值计算  
参数说明: src表示当前图片

sobel算子：分为x轴方向和y轴方向上的，  
x轴方向上的算子如图中的Gx，将sober算子在图中进行平移，当前位置的像素值等于sobel算子与(当前位置与周边位置8个点)进行对应位置相乘并相加操作，作为当前位置的像素点（右减左）  
y轴方向的算子如Gy， 对于x轴方向上，即左右两边的比较（下减上）

计算方程为：x轴： p3 - p1 + 2 \* p6 - 2 \* p4 + p9 - p7， 右边的像素值减去左边的像素值  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731234028464.png)

```
import cv2
import matplotlib.pyplot as plt
img = cv2.imread('pie.png',cv2.IMREAD_GRAYSCALE)
sobelx = cv2.Sobel(img,cv2.CV_64F,1,0,ksize=3)
sobelxx = cv2.convertScaleAbs(sobelx)

sobely = cv2.Sobel(img,cv2.CV_64F,0,1,ksize=3)
sobelyy = cv2.convertScaleAbs(sobely)


sobelxy_1 = cv2.addWeighted(sobelxx,0.5,sobelyy,0.5,0)

sobelxy_2 = cv2.Sobel(img,cv2.CV_64F,1,1,ksize=3)


cv2.imshow('sobelx',sobelx)
cv2.imshow('sobelxx',sobelxx)
cv2.imshow('sobely',sobely)
cv2.imshow('sobelyy',sobelyy)
cv2.imshow('sobelxy_1',sobelxy_1)
cv2.imshow('sobelxy_2',sobelxy_2)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

> 白到黑是正数，黑到白就是负数了，所有的负数会被截断成0，所以要用convertScalerAbs取绝对值

注：图sobelx，右半边也有梯度，但是是从黑到白，所以为0(黑)，所以看不出来。（图sobely的下半部分同理）

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200731235846402.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200801000105472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200801000205987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020080100001633.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
Lena图像的sobel水平竖直方向的操作效果  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200801001825188.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200801003628188.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
**laplacian算子**计算方程为：p2 + p4 + p6 +p8 - 4 \* p5

Scharr算子和Sobel算子的用法相同，Laplacian算子就不存在x y了

```
img = cv2.imread('lena.jpg',cv2.IMREAD_GRAYSCALE)
sobelx = cv2.Sobel(img,cv2.CV_64F,1,0,ksize=3)
sobely = cv2.Sobel(img,cv2.CV_64F,0,1,ksize=3)
sobelx = cv2.convertScaleAbs(sobelx)   
sobely = cv2.convertScaleAbs(sobely)  
sobelxy =  cv2.addWeighted(sobelx,0.5,sobely,0.5,0)  

scharrx = cv2.Scharr(img,cv2.CV_64F,1,0)
scharry = cv2.Scharr(img,cv2.CV_64F,0,1)
scharrx = cv2.convertScaleAbs(scharrx)   
scharry = cv2.convertScaleAbs(scharry)  
scharrxy =  cv2.addWeighted(scharrx,0.5,scharry,0.5,0) 

laplacian = cv2.Laplacian(img,cv2.CV_64F)
laplacian = cv2.convertScaleAbs(laplacian)   

res = np.hstack((img,sobelxy,scharrxy,laplacian))
cv_show(res,'res')
```

可以看到，Scharr算子比Sobel算子更敏感，捕获更多细节，更丰富  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200801004101222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## Canny边缘检测

```
img=cv2.imread("lena.jpg",cv2.IMREAD_GRAYSCALE)

v1=cv2.Canny(img,80,150)
v2=cv2.Canny(img,50,100)

res = np.hstack((img,v1,v2))
cv_show(res,'Canny_res')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020080101352653.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

```
img=cv2.imread("car.png",cv2.IMREAD_GRAYSCALE)
v1=cv2.Canny(img,120,250)
v2=cv2.Canny(img,50,100)
res = np.hstack((img,v1,v2))
cv_show(res,'res')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200801013848662.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 图像金字塔

图像金字塔是一组图像的集合，集合中的所有图像都是通过对某一图像连续降采样得到的一组图像序列。

-   cv2.pyrUp: 上采样
-   cv2.pyrDown: 下采样

有两种经典的金字塔：**高斯金字塔**和**拉普拉斯金字塔**，前者采用向下采样，后者是向上采样需要的缺失的信息。

**向下采样**(生成高斯金字塔)的具体操作为： 从大到小  
　　　　1. 对图像进行高斯卷积  
　　　　2. 删除所有的偶数行和偶数列

**向上采样**的缺失信息(生成拉普拉斯金字塔)的具体操作为：从小到大  
　　　　1. 首先将维数扩大两倍  
　　　　2. 将扩大位的值置为0  
　　　　　　　　　　　　3. 对新的图像进行高斯卷积  
　　　　　　　　　　　　　　　　4. 用新的层次的高斯金字塔减去 3 中形成的图像  
　　　　　　　　　　　　　　　　　　　　　　　　![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806123741540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

```
import cv2
import numpy as np
img = cv2.imread('AM.png')
up = cv2.pyrUp(img)
down = cv2.pyrDown(img)
cv2.imshow('img',img)
cv2.imshow('up',up)
cv2.imshow('down',down)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806125512749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
先上采样，再下采样，不能恢复原图的清晰度

```
up=cv2.pyrUp(img)
up_down=cv2.pyrDown(up)
cv_show(up_down,'up_down')
cv_show(np.hstack((img,up_down)),'up_down')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/202008061243089.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

-   拉普拉斯金字塔  
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806130550413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

```
cv_show(img-up_down,'img-up_down')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806130046883.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 图像轮廓

-   cv2.findContours(img,mode,method)  
    **img: 二值图像**  
    **mode: 轮廓检索模式**
-   RETR\_EXTERNAL ：只检索最外面的轮廓；
-   RETR\_LIST：检索所有的轮廓，并将其保存到一条链表当中；
-   RETR\_CCOMP：检索所有的轮廓，并将他们组织为两层：顶层是各部分的外部边界，第二层是空洞的边界;
-   **RETR\_TREE**：检索所有的轮廓，并重构嵌套轮廓的整个层次; （通常选这个）

**method:轮廓逼近方法**

-   CHAIN\_APPROX\_NONE：以Freeman链码的方式输出轮廓，所有其他方法输出多边形（顶点的序列）。
-   CHAIN\_APPROX\_SIMPLE:压缩水平的、垂直的和斜的部分，也就是，函数只保留他们的终点部分。

**返回值**

-   contours 是一个list，list中每个元素都是图像中的一个轮廓，用numpy中的ndarray表示
-   hierarchy 是一个ndarray，其中的元素个数和轮廓个数相同，每个轮廓contours\[i\]对应4个hierarchy元素hierarchy\[i\]\[0\] ~hierarchy\[i\]\[3\]，分别表示后一个轮廓、前一个轮廓、父轮廓、内嵌轮廓的索引编号，如果没有对应项，则该值为负数

> 如果报错  
> binary, contours, hierarchy = cv2.findContours(thresh, cv2.RETR\_TREE, cv2.CHAIN\_APPROX\_NONE)  
> ValueError: not enough values to unpack (expected 3, got 2)  
> .
> 
> 要想返回三个参数：  
> 把OpenCV 降级成3.4.3.18 就可以了，在终端输入pip install opencv-python==3.4.3.18  
> OpenCV 新版返回两个参数：  
> contours, hierarchy = cv2.findContours(thresh, cv2.RETR\_TREE, cv2.CHAIN\_APPROX\_NONE)  
> .  
> 查看opencv版本：print(cv2.**version**)  
> 我的opecv版本为： 4.2.0

```
img = cv2.imread('contours.png')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)


contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_NONE)


draw_img1 = img.copy()
draw_img2 = img.copy()
draw_img3 = img.copy()
all_contours = cv2.drawContours(draw_img1, contours, -1, (0, 0, 255), 2)
contours_0 = cv2.drawContours(draw_img2,contours,0,(0,0,255),2)
contours_1 = cv2.drawContours(draw_img3,contours,1,(0,0,255),2)
cv_show('res',np.hstack((all_contours,contours_0,contours_1)))
```

### 绘制轮廓

cv2.drawContours  
参数如下：传入绘制图像，轮廓，轮廓索引，颜色模式，线条厚度  
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020080614312080.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 轮廓特征

```
cnt = contours[0]

cv2.contourArea(cnt)


cv2.arcLength(cnt,True)
```

### 轮廓近似

-   cv2.approxPolyDP(contour,epsilon,True) 把一条平滑的曲线曲折化  
    参数  
    epsilon：表示的是精度，越小精度越高，因为表示的意思是是原始曲线与近似曲线之间的最大距离  
    closed：表示输出的多边形是否封闭；true表示封闭，false表示不封闭。

> 算法步骤 ：
> 
> 1.  连接曲线首尾两点A、B形成一条直线AB； 计算曲线上离该直线段距离最大的点C，计算其与AB的距离d；
> 2.  比较该距离与预先给定的阈值threshold的大小，如果小于threshold，则以该直线作为曲线的近似，该段曲线处理完毕。
> 3.  如果距离大于阈值，则用点C将曲线分为两段AC和BC，并分别对两段曲线进行步骤\[1~3\]的处理。
> 4.  当所有曲线都处理完毕后，依次连接各个分割点形成折线，作为原曲线的近似。  
>     ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806143851983.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020080616111014.png)

```
img = cv2.imread('contours2.png')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)

contours,hierarchy = cv2.findContours(thresh,cv2.RETR_TREE,cv2.CHAIN_APPROX_NONE)
cnt = contours[0]
epsilon1 = 0.05*cv2.arcLength(cnt,True) 
epsilon2 = 0.1*cv2.arcLength(cnt,True) 
epsilon3 = 0.15*cv2.arcLength(cnt,True) 
approx1 = cv2.approxPolyDP(cnt,epsilon1,True)
approx2 = cv2.approxPolyDP(cnt,epsilon2,True)
approx3 = cv2.approxPolyDP(cnt,epsilon3,True)

draw_img1 = img.copy()
draw_img2 = img.copy()
draw_img3 = img.copy()

approx1_res = cv2.drawContours(draw_img1, [approx1], -1, (0, 0, 255), 2)
approx2_res = cv2.drawContours(draw_img2, [approx2], -1, (0, 0, 255), 2)
approx3_res = cv2.drawContours(draw_img3, [approx3], -1, (0, 0, 255), 2)
cv_show('res',np.hstack((approx1_res,approx2_res,approx3_res)))

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806151123542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

-   cv2.boundingRect：矩形边框(Bounding Rectangle)是用一个最小的矩形，把找到的形状包起来  
    返回四个值，分别是x，y，w，h；  
    x，y是矩阵左上点的坐标，w，h是矩阵的宽和高
-   cv2.rectangle(img, (x,y), (x+w,y+h), (0,255,0), 2)画出矩行  
    第一个参数：img是原图  
    第二个参数：(x,y)是矩阵的左上点坐标  
    第三个参数：(x+w,y+h)是矩阵的右下点坐标  
    第四个参数：(0,255,0)是画线对应的rgb颜色  
    第五个参数：2是所画的线的宽度
-   cv2.minAreaRect()：得到包覆轮廓的最小斜矩形，
-   cv2.minEnclosingCircle()：得到包覆此轮廓的最小圆形  
    返回一个二元组，第一个元素为圆心坐标组成的元组，第二个元素为圆的半径值。
-   cv2.circle(img, center, radius, color, thickness, lineType, shift) 根据给定的圆心和半径等画圆  
    参数说明  
    img：输入的图片data  
    center：圆心位置  
    radius：圆的半径  
    color：圆的颜色  
    thickness：圆形轮廓的粗细（如果为正）。负厚度表示要绘制实心圆。  
    lineType： 圆边界的类型。  
    shift：中心坐标和半径值中的小数位数。

```

img = cv2.imread('contours.png')
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ret, thresh = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)
contours, hierarchy = cv2.findContours(thresh, cv2.RETR_TREE, cv2.CHAIN_APPROX_NONE)
cnt_2 = contours[2]

x,y,w,h = cv2.boundingRect(cnt_2)
img_rec = cv2.rectangle(img,(x,y),(x+w,y+h),(0,255,0),2)

cv_show('img_rec',img_rec)

area = cv2.contourArea(cnt)
rect_area = w * h
extent = float(area) / rect_area

```

```

cnt_8 = contours[8]
(x,y),radius = cv2.minEnclosingCircle(cnt_8) 
center = (int(x),int(y)) 
radius = int(radius) 
img_cir = cv2.circle(img,center,radius,(0,255,255),2)
cv_show('img_cir',img_cir)
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806162131500.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 模板匹配

模板匹配和卷积原理很像,模板在原图像上从原点开始滑动,计算模板与(图像被模板覆盖的地方)的差别程度,这个差别程度的计算方法在opencv里有6种,然后格每次计算的結果放入一个矩阵里,作为結果输出.假如原图形是AxB大小,而模板是axb大小,则输出结果的矩阵是(A-a+1)x(B-b+1)

简单来说，模板匹配就是在整个图像区域发现与给定子图像匹配的小块区域。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806171341132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

-   **cv2.matchTemplate**(image, templ, method, result=None, mask=None)  
    image：待搜索图像  
    templ：模板图像  
    method：计算匹配程度的方法  
    返回参数res：是一个结果矩阵，假设待匹配图像为 I，宽高为(W,H)，模板图像为 T，宽高为(w,h)。那么result的大小就为(W-w+1, H-h+1)

> 其中method：  
> TM-SQDIFF:计算平方不同,计算出来的值越小,越相关  
> TM\_CCORR:计算相关性,计算出来的值越大,越相关  
> TM\_CCOEFF:计算相关系数,计算出来的值越大,越相关  
> TM SQDIFF-NORMED: 计算归一化平方不同,计算出来的值越接近0,越相关  
> TM\_CCORR-NORMED: 计t算归一化相关性,计算出来的值越接近1,越相关  
> TM-CCOEFF-NORMED:计算归一化相关系数,计算出来的值越接近1,越相关  
> 公式：https://docs.opencv.org/3.3.1/df/dfb/group\_\_imgproc\_\_object.html#ga3a7850640f1fe1f58fe91a2d7583695d  
> https://blog.csdn.net/a906958671/article/details/89551856

-   **cv2.minMaxLoc**(res)  
    输入矩阵res  
    min\_val, max\_val, min\_loc, max\_loc是这个矩阵的最小值，最大值，最大值的索引，最小值的索引

```
import cv2
import numpy as np
from matplotlib import pyplot as plt
%matplotlib inline
img = cv2.imread('touxiang.jpg',0)
img2 = img.copy()
template = cv2.imread('template.jpg',0)
w, h = template.shape[::-1]


methods = ['cv2.TM_CCOEFF', 'cv2.TM_CCOEFF_NORMED', 'cv2.TM_CCORR',
'cv2.TM_CCORR_NORMED', 'cv2.TM_SQDIFF', 'cv2.TM_SQDIFF_NORMED']

for meth in methods:
    img = img2.copy()
    method = eval(meth)

    
    res = cv2.matchTemplate(img,template,method)
    min_val, max_val, min_loc, max_loc = cv2.minMaxLoc(res)

    
    if method in [cv2.TM_SQDIFF, cv2.TM_SQDIFF_NORMED]:
        top_left = min_loc
    else:
        top_left = max_loc
    bottom_right = (top_left[0] + w, top_left[1] + h)

    cv2.rectangle(img,top_left, bottom_right, 255, 2)

    plt.subplot(121),plt.imshow(res,cmap = 'gray')
    plt.title('Matching Result'), plt.xticks([]), plt.yticks([])
    plt.subplot(122),plt.imshow(img,cmap = 'gray')
    plt.title('Detected Point'), plt.xticks([]), plt.yticks([])
    plt.suptitle(meth)
    plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806215250622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806215324642.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200806215409278.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
**匹配多个对象**

```
img_rgb = cv2.imread('mario.jpg')
img_gray = cv2.cvtColor(img_rgb, cv2.COLOR_BGR2GRAY)
template = cv2.imread('mario_coin.jpg', 0)
h, w = template.shape[:2]

res = cv2.matchTemplate(img_gray, template, cv2.TM_CCOEFF_NORMED)
threshold = 0.8

loc = np.where(res >= threshold)
for pt in zip(*loc[::-1]):  
    bottom_right = (pt[0] + w, pt[1] + h)
    cv2.rectangle(img_rgb, pt, bottom_right, (0, 0, 255), 2)

cv2.imshow('img_rgb', img_rgb)
cv2.waitKey(0)
```

## 直方图

直方图（histogram）是灰度级的函数，描述的是图像中每种灰度级像素的个数，反映图像中每种灰度出现的频率。横坐标是灰度级，纵坐标是灰度级出现的频率

-   cv2.calcHist(images,channels,mask,histSize,ranges)  
    images: 原图像图像格式为 uint8 或 ﬂoat32。当传入函数时应 **用中括号 \[\]** 括来例如\[img\]  
    channels: 同样用中括号括来它会告函数我们统幅图 像的直方图。如果入图像是灰度图它的值就是 \[0\]如果是彩色图像 的传入的参数可以是 \[0\]\[1\]\[2\] 它们分别对应着 BGR。  
    mask: 掩模图像。统整幅图像的直方图就把它为 None。但是如 果你想统图像某一分的直方图的你就制作一个掩模图像并 使用它。  
    histSize:BIN 的数目。也应用中括号括来  
    ranges: 像素值范围常为 \[0-256\]

```
import cv2 
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline 
def cv_show(img,name):
    cv2.imshow(name,img)
    cv2.waitKey()
    cv2.destroyAllWindows()

img = cv2.imread('cat.jpg',0) 
hist = cv2.calcHist([img],[0],None,[256],[0,256])


plt.hist(img.ravel(),256); 
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810012609815.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
分别显示3个颜色通道的直方图

```
img = cv2.imread('cat.jpg') 
color = ('b','g','r')
for i,col in enumerate(color): 
    histr = cv2.calcHist([img],[i],None,[256],[0,256]) 
    plt.plot(histr,color = col) 
    plt.xlim([0,256]) 
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810012651749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### mask 操作

```
img = cv2.imread('cat.jpg', 0)


mask = np.zeros(img.shape[:2], np.uint8)
mask[100:300, 100:400] = 255
cv_show(mask,'mask')

masked_img = cv2.bitwise_and(img, img, mask=mask)
hist_full = cv2.calcHist([img], [0], None, [256], [0, 256])
hist_mask = cv2.calcHist([img], [0], mask, [256], [0, 256])

plt.subplot(221), plt.imshow(img, 'gray')
plt.subplot(222), plt.imshow(mask, 'gray')
plt.subplot(223), plt.imshow(masked_img, 'gray')
plt.subplot(224), plt.plot(hist_full), plt.plot(hist_mask)
plt.xlim([0, 256])
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020081001394459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 直方图均衡化

```
img = cv2.imread('cat.jpg',0) 
equ = cv2.equalizeHist(img) 

res = np.hstack((img,equ))
cv_show(res,'res')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810015221470.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)  
自适应直方图均衡化

```
clahe = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8,8)) 
res_clahe = clahe.apply(img)
res = np.hstack((img,equ,res_clahe))
cv_show(res,'res')
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810015656432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

## 傅里叶变换

我们生活在时间的世界中，早上7:00起来吃早饭，8:00去挤地铁，9:00开始上班。。。以时间为参照就是时域分析。  
但是在频域中一切都是静止的！

https://zhuanlan.zhihu.com/p/19763358  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810020606454.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

-   傅里叶变换的作用  
    高频：**变化剧烈**的灰度分量，例如边界  
    低频：**变化缓慢**的灰度分量，例如一片大海  
    在原图中做低频/高频的变换较难，因此转换到频域中处理较方便
    
-   滤波  
    低通滤波器：只保留低频，会使得图像模糊  
    高通滤波器：只保留高频，会使得图像细节增强
    

opencv中主要就是 **cv2.dft()** 和 **cv2.idft()** ，输入图像需要**先转换成np.float32 格式**。  
得到的结果中频率为0的部分会在左上角，通常要转换到中心位置，可以通过shift变换来实现。  
cv2.dft()返回的结果是双通道的（实部，虚部），通常还需要转换成图像格式才能展示（0,255）, 用逆变换cv2.idft()。

```
img = cv2.imread('lena.jpg',0)

img_float32 = np.float32(img)

dft = cv2.dft(img_float32, flags = cv2.DFT_COMPLEX_OUTPUT)

dft_shift = np.fft.fftshift(dft)

magnitude_spectrum = 20*np.log(cv2.magnitude(dft_shift[:,:,0],dft_shift[:,:,1]))

plt.subplot(121),plt.imshow(img, cmap = 'gray')
plt.title('Input Image'), plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(magnitude_spectrum, cmap = 'gray')
plt.title('Magnitude Spectrum'), plt.xticks([]), plt.yticks([])
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810023421557.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 低通滤波

```
import numpy as np
import cv2
from matplotlib import pyplot as plt

img = cv2.imread('lena.jpg',0)

img_float32 = np.float32(img)

dft = cv2.dft(img_float32, flags = cv2.DFT_COMPLEX_OUTPUT)

dft_shift = np.fft.fftshift(dft)

rows, cols = img.shape
crow, ccol = int(rows/2) , int(cols/2)     


mask = np.zeros((rows, cols, 2), np.uint8)
mask[crow-30:crow+30, ccol-30:ccol+30] = 1


fshift = dft_shift*mask
f_ishift = np.fft.ifftshift(fshift)
img_back = cv2.idft(f_ishift)
img_back = cv2.magnitude(img_back[:,:,0],img_back[:,:,1])

plt.subplot(121),plt.imshow(img, cmap = 'gray')
plt.title('Input Image'), plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(img_back, cmap = 'gray')
plt.title('Result'), plt.xticks([]), plt.yticks([])

plt.show() 
```

低通滤波损失了边界信息  
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810024211717.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

### 高通滤波

同理，则代码不详细备注  
仅mask\[crow-30:crow+30, ccol-30:ccol+30\] = 0与上面不同  
因为保留高频

```
img = cv2.imread('lena.jpg',0)
img_float32 = np.float32(img)

dft = cv2.dft(img_float32, flags = cv2.DFT_COMPLEX_OUTPUT)
dft_shift = np.fft.fftshift(dft)

rows, cols = img.shape
crow, ccol = int(rows/2) , int(cols/2)     


mask = np.ones((rows, cols, 2), np.uint8)
mask[crow-30:crow+30, ccol-30:ccol+30] = 0


fshift = dft_shift*mask
f_ishift = np.fft.ifftshift(fshift)
img_back = cv2.idft(f_ishift)
img_back = cv2.magnitude(img_back[:,:,0],img_back[:,:,1])

plt.subplot(121),plt.imshow(img, cmap = 'gray')
plt.title('Input Image'), plt.xticks([]), plt.yticks([])
plt.subplot(122),plt.imshow(img_back, cmap = 'gray')
plt.title('Result'), plt.xticks([]), plt.yticks([])
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200810024414528.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI5OTUwNzAz,size_16,color_FFFFFF,t_70)

【该系列笔记侧重于代码展示，各个知识点的详细原理可以另外查询】