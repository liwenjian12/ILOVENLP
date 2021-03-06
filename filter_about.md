# Opencv中的滤波器

<!--TOC-->
- [滤波的目的和主要原理](#滤波的目的和主要原理)
- [滤波过程](#滤波过程)
  - [核](#核)
  - [滤波类型](#滤波类型)
   - [方框滤波器](#方框滤波器)
   - [均值滤波器](#均值滤波器)
   - [高斯滤波器](#高斯滤波器)
   - [中值滤波器](#中值滤波器)
   - [双边滤波](#双边滤波)
  
<!--/TOC-->

## 滤波的目的和主要原理
**图像的滤波目的有两个：**
1、是抽出图像的特征作为图像识别的特征模式；
2、是为适应图像处理的要求，消除图像数字化时所混入的噪声；（** 图像平滑 **)

- 图像的平滑处理：平滑滤波是指低频增强的空间滤波技术。主要目的是模糊和消除噪音；

- 常用的5种**图像平滑处理操作方法**，方框滤波——boxFilter函数，均值滤波——Blur函数，高斯滤波——GaussianBlur函数，中值滤波——medianBlur函数，双边滤波——bilateralFilter函数。
- 前三种都属于线性滤波，后两种属于非线性滤波；   
- 滤波和模糊：滤波是将信号中特定波段频率滤除的操作，是抑制和防止干扰的一项重要措施。模糊是滤波的一种。高斯滤波是指用高斯函数作为滤波函数的滤波操作。高斯模糊就是高斯低通滤波。

    图像平滑(smoothing)也称为图像模糊(blurring),是一种在图像处理中使用频率很高的操作，进行图像平滑的操作原因有很多，在这里重点介绍使用平滑操作降低图片噪声。因为在图像中，噪声的能量大都集中在幅度谱的低频和中频部分，而在较高的频段，一些重要的细节信息往往被噪声淹没。在一幅图像中，所谓的高频部分是指图像中像素值落差很大的部分，而低频则是指像素值与旁边的像素值相差不大甚至相同，而图像的一些细节的部分往往由高频信息来展现，图像中掺杂的噪声往往也处于高频段，这就造成了一些细节信息被噪声淹没，可以根据不同的噪声类型用不同的滤波器进行处理。

## 滤波过程
为了进行图像平滑操作，通常在图像上加一个滤波器(filter)，最常见的类型是线性的，输出像素值(g(i, j))最终由原像素值和加权值决定。其公式如下：

 ![内容自填](https://github.com/liwenjian12/opencv/blob/master/pictures/20161225144553744.jpg)
 
 其中h(k, l)被称为核(kernel),是加到图像上滤波器(filter)的系数，它有助于把滤波器进行可视化为一个窗口在图像上滑动，这些设计到邻域的卷积操作。
 
![内容自填](https://github.com/liwenjian12/opencv/blob/master/pictures/20161225145943342.jpg)

## 滤波类型
### 方框滤波器
这是所有滤波器中最简单的一种滤波方式。每一个输出像素的是内核邻域像素值的平均值得到。通用的滤波kernel如下： 

![内容自填](https://github.com/liwenjian12/opencv/blob/master/pictures/20161225150807916.jpg)

这里是一个长宽分别为Kwidth和Kheight的窗口函数，在此区域内邻域中像素值叠加求平均即可求出位于kernel中心点像素的像素值。opencv中提供了方框滤波函数boxFilter()，Kwidth和Kheight 代表box的宽和高。
```
C++: void boxFilter(InputArray src, OutputArray dst, int ddepth, Size ksize, Point anchor=Point(-1,-1), bool normalize=true, int borderType=BORDER_DEFAULT )
. InputArray src: 输入图像，可以是Mat类型
. OutputArray dst: 经滤波后输出图像
. int ddepth: 目标图像的深度，若设置为-1,则深度与原图像深度相同
. Size ksize: Size类型，内核的大小，一般用Size(w, h)表示，如Size(3, 3)表示kernel窗口大小为3x3
. Point anchor = Point(-1,-1): 进行滤波操作的点，如果是默认值(-1, -1)说明对上述窗口中心点所对应的像素点进行操作
. bool normalize = true: 内核是否被归一化处理，有默认值true
. int borderType = BORDER_DEFAULT: 用于腿短图像外部像素的某种便捷模式，有默认值BORDER_DEFAULT. 
```
方框滤波用的kernel如下：

![](https://github.com/liwenjian12/opencv/blob/master/pictures/20161225152419921.jpg)

![](https://github.com/liwenjian12/opencv/blob/master/pictures/20161225152444593.jpg)

### 均值滤波器
这是最简单的均值滤波器，可以去除均匀噪声和高斯噪声，但会对图像造成一定程度的模糊。
令Sxy表示中心点在(x,y)处，大小为m×n的滤波器窗口。算术均值滤波器就是简单的计算窗口区域的像素均值，然后将均值赋值给窗口中心点处的像素。

### 高斯滤波器
高斯滤波则不同，其能够很好的消除噪声。高斯滤波过程中，每个像素点都是由本身和领域内的其他像素值经过加权平均后得到的。
从数学的角度来看，图像的高斯模糊过程就是图像与正态分布做卷积，由于正态分布也被称之为高斯分布，因此这项技术被称为高斯模糊。
由于高斯函数的傅里叶变换是另外一个高斯函数，所以**高斯模糊对图像来说就是一个低通滤波器**。

### 中值滤波器
中值滤波的基本思想是用像素点领域灰度值的中值来代替该像素的灰度值，该方法在去除脉冲噪声、椒盐噪声的同时还能保留图像的细节部分。
中值滤波花费的时间比均值滤波更久，但其在噪声的消除能力上更强。

### 双边滤波
双边滤波是结合图像的空间邻近度和像素值相似度的一种折中处理，同时考虑空间域信息和灰度相似性，达到保留边缘且去除噪声的目的。 
![](https://github.com/liwenjian12/opencv/blob/master/pictures/20151208134222591.jpg)

## Opencv中的相关函数
```
#include<opencv2\opencv.hpp>
#include<iostream>
#include<math.h>
#include<algorithm>
using namespace std;
using namespace cv;

int main()
{
  mat src;
  src = imread("1.jpg",1);
  if (src.empty())
  {
    printf("cannot load !! \n");
    return -1;
  }
  namedWindow("原图");
  imshow("",src);
  Mat dst,dst1;
  blur(src,dst,Size(3,3), Point(-1, -1));  # 均值滤波
  
  GaussianBlur(src, dst, Size(5,5),5,5);   # 高斯滤波
  
  medianBlur(src, dst, 5);                 # 中值滤波  
  
  bilateralFilter(src, dst, 5, 100, 3);    # 双边滤波
}
```
