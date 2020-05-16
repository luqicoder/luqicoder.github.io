---
title: OpenCv入门之cvMat矩阵
tags:
  - opencv
categories:
  - 图像处理
date: 2020-01-09 17:17:52
---



### OpenCV库模块

> **opencv_core** 模块包含库的核心功能，定义了图像数据结构的核心头文件
>
> **opencv_imgproc** 模块 包含主要的图像处理函数
>
> **opencv_highgui** 模块提供了读写图像和视频的函数以及一些用户交 互函数。
> 也可以只包含**opencv2/opencv.hpp**这个头文件，它其中包含了基本上常用的的opencv头文件。

<!--more-->

### imread()函数

> **cv::imread**()第二个参数用于设置读取图片模式
>
> **cv::IMREAD_GRAYSCALE**(等价于0)：以灰度模式读入图像，生成的图像由无符号字节构成(C++中为unsigned char)，opencv中用常量表示为：CV_8U。
>
> **cv::IMREAD_COLOR**(等价于正数)：将读取图像转换为3通道彩色图像(创建的图像每个像素有3字节，opecv中用CV_8UC3表示，如果输入图像本身为灰图像，三个通道值就是一样的。)。图像的透明度会被忽略，默认参数。
>
> **cv::IMREAD_UNCHANGED**(等价于负数)：读入一幅图像，并且包括图像的 alpha 通道。
>
> **注意**：使用imshow 显示由整数（CV_16U 表示 16位无符号整数，CV_32S 表示 32位有符号整数）构成的图像时，图像每个像素的值会被除以 256，以便能够在 256级灰度中显示。同样，在 显示由浮点数构成的图像时，值的范围会被假设为 0.0（显示黑色）~1.0（显示白色）。超出这个 范围的值会显示为白色（大于 1.0的值）或黑色（小于 0.0的值）。

### 实例-对图像的简单操作

``` C++
#include <iostream>
#include <opencv2/core.hpp>
#include <opencv2/highgui.hpp>
int main()
{
	//创建一副0*0的空图像
	cv::Mat image; 
	std::cout << "the image's rows:" << image.rows << std::endl << "the image's cols:" << image.cols << std::endl << "the image's size:" << image.size() << std::endl;
	//重新读取一幅图像
	image = cv::imread("D:\\code_space\\images\\test.jpg");
	//错误处理
	if (image.empty())
	{
		std::cout << "未能加载图像！" << std::endl;
		return 0;
	}
	std::cout << "the image's rows:" << image.rows << std::endl << "the image's cols:" << image.cols << std::endl << "the image's size:" << image.size() << std::endl;
	//图像显示
	cv::imshow("Show Window", image);
	//也可自定义窗口名字
	cv::namedWindow("Show Window2");
	//由于控制台窗口下，main()函数结束时就会关闭，因此需要额外增加一个highgui函数，待用户键入数值后结束程序
	//0表示永远等待按键，键入正数表示等待毫秒数
	cv::waitKey(0);
	//图像处理
	cv::Mat result;
	//cv::flip(src,dst,orientation)用于图像翻转
	//orientation,=0表示垂直,正数表示水平，负数表示水平与垂直
	cv::flip(image, result, 0);
	cv::imshow("0", result);
	cv::flip(image, result, 1);
	cv::imshow("1", result);
	cv::flip(image, result, -1);
	cv::imshow("-1", result);
	cv::waitKey(0);
	//图像保存
	cv::imwrite("-1.jpg", result);
	return 0;
}
```