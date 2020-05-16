---
title: OpenCv入门之cvMat矩阵
tags:
  - opencv
categories:
  - 图像处理
date: 2020-01-09 17:00:36
typora-root-url: ../../site/source/
---

在opencv中，加载的图像数据存储在cv:Mat这个类中。

<!--more-->

### Mat类的构造函数:

#### **Mat(int rows,int cols,int type)**

> - rows 代表矩阵的行数
> - cols 代表矩阵的列数
> - type 代表类型，包括通道数及其 数据类型，
>
> type可以设置为 CV_8UC(n），CV_8SC(n）， CV 16SC(n），CV 16UC(n）， CV_32SC(n）， CV_32FC(n）， CV_64FC(n)， 其中 8U 、 8S 、 16S 、 16U 、 32S 、 32F 、 64F 前面的数字代表 Mat 中每一个数值所占的 bit 数，根据 1byte=8bit ，即 32F 就是占 4 字节的 float 类型， 64F是占 8 字节的 doule 类型， 32S 是占 4 字节的 int 类型 ， 8U 是占 1 字节的 uchar 类型，其他的类似；（u代表无符号，s代表有符号） ，C(n）代表通道数，当 n = 1 时，构造单通道矩阵或称二维矩阵，当 n > 1 时，即构造多通 道矩阵即三维矩阵。
>

> 另一种 Mat 构造函数：
>

#### **Mat(Size(int cols, int rows), int type)**

> 使用 OpenCV 的 Size 类，该类一般用来存储矩阵的列数和行数。
>
> *注意Size 的第一个元素是矩阵的列数（宽）．第二个元素是矩阵的行数（高）*，与上面构造函数相反。
>
> cv::Mat有两个必不可少的组成部分，头部与数据块。
>
> 头部包含矩阵的相关信息(大小，通道数量，数据类型等)
>

### 实例：

``` C++
//读取一副图像并将其转换为灰度图像
cv::Mat image = cv::imread("D:\\code_space\\images\\test.jpg");
//分别显示行数，列数，尺寸，通道数，数据类型
std::cout << image.cols << std::endl
 <<image.rows << std::endl
 << image.size() << std::endl
 << image.channels() << std::endl
    << image.type() << std::endl;
```

> cv::Mat头部有一个指向数据块的指针，即data属性。
> ***在实际情况中，大多数操作仅复制了cv::Mat的头部，因此多个对象指向同一个数据块***。
> 注：cv::Mat只有在明确要求时，内存块才会被复制。

``` C++
//创建一个240行，320列的单通道图像
cv::Mat image(240, 320, CV_8U, 100);
cv::Mat image1(cv::Size(320, 240), CV_8U, 100);
std::cout << image.rows << std::endl << image1.rows;
//创建一副彩色图像
//并且通过cv::Scalar数据结构设置像素值，通道次序BGR
cv::Mat image2(240, 320, CV_8UC3, cv::Scalar(0, 0, 255));

//如果初始化灰度图像
cv::Mat image3(240, 320, CV_8UC1, cv::Scalar(100));
//创建一副未初始化彩色图像
cv::Mat image4(240, 320, CV_8UC3);
//创建一副未初始化灰度图像
cv::Mat image5(240, 320, CV_8U);
cv::imshow("image2", image2);
cv::imshow("image3", image3);
cv::imshow("image4", image4);
cv::imshow("image5", image5);
cv::waitKey(0);
```

> 注：如果你对C++初始化不理解可以参考[这篇文章](https://blog.csdn.net/twdlll/article/details/78265348)。
> 另外可以通过**creat()**函数对一个矩阵重新赋值。

``` C++
//重新分配一个新图像，
//仅在大小或者类型不同时
image1.create(200, 200, CV_8U);
```

> **注意:**如果新图像的尺寸与原图像相同，就不会重新分配内存。
>
> **采用浅复制进行图像复制**

```C++
//所有图像都指向一个数据块，即同一块内存区域
cv::Mat image(200, 300, CV_8UC1, 255);
cv::Mat image2(image);cv::Mat image3 = image;
```

> **另一种方式，需要另外分配内存空间。即深复制**

```C++
cv::Mat image4 = image.clone();
cv::Mat image5;image5.copyTo(image);
```

> 如果将一幅图像复制到另一幅图像，且更改数据类型。
>

``` C++
//转成浮点型图像[0,1]
//后两个为可选参数，缩放比例与偏移量，注意这两幅图像通道数量一样。
image.convertTo(image2, CV_32F, 1 / 255.0, 0.0);
```

![头像](/images/avatar.jpg)

