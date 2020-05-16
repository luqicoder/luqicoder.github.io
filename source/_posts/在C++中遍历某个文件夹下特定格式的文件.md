---
title: C++中遍历某个文件夹下特定格式的文件
date: 2020-01-07 20:21:57
categories: 
- C++
tags:
- 文件操作
---

### 前言

最近写C++程序时，需要用到遍历某个文件夹下特定格式的文件。网上参考了许多代码，这里找到了知乎上一篇比较详细的文章介绍。参考了他的代码，但是源代码不能使用，下面做出修改。

 <!-- more -->

系统环境：win10+vs2019

参考：https://zhuanlan.zhihu.com/p/64917376

### struct _finddata_t结构体

这里主要利用windows API

Struct _finddata_t是用来存储文件各种信息的结构体，使用这个结构体要引用的头文件为“ #include <io.h>”它的结构体定义如下：

**struct _finddata_t {**

**unsigned attrib;**

**time_t time_create;**

**time_t time_access;**

**time_t time_write;**

**_fsize_t size; char name[_MAX_FNAME];**

**};**

**unsigned atrrib**：文件属性的存储位置。它存储一个unsigned单元，用于表示文件的属性。文件属性是用位表示的， 主要有以下一些：_A_ARCH（存档）、 _A_HIDDEN（隐藏）、_A_NORMAL（正常）、_A_RDONLY（只读）、_A_SUBDIR（文件夹）、_A_SYSTEM（系统）。 这些都是在中定义的宏，可以直接使用，而本身的意义其实是一个无符号整型（只不过这个整型应该是2的几次幂，从而保证只有一位为 1，而其他位为0）。 既然是位表示，那么当一个文件有多个属性时，它往往是通过位或的方式，来得到几个属性的综合。 例如只读+隐藏+系统属性，应该为：_A_HIDDEN | _A_RDONLY | _A_SYSTEM 。

**time_t time_create**：这里的time_t是一个变量类型，实际上就是长整形变量 long int，用来保存从1970年1月1日0时0分0秒到现在时刻的秒数

**time_t time_access**：文件最后一次被访问的时间。

**time_t time_write**：文件最后一次被修改的时间。

**_fsize_t size**：文件的大小（字节数表示）。

**char name[_MAX_FNAME]**：文件的文件名。这里的_MAX_FNAME是一个常量宏，它在头文件中被定义，表示的是文件名的最大长度。

如何使用这个结构体才能够将文件的信息存储到该结构体的内存空间呢，这就需要_findfirst（）、_findnext（）和_fineclose（）三个函数的搭配使用，下面介绍这三个函数：

> **long _findfirst( char \*filespec, struct _finddata_t \*fileinfo )；**

返回值：如果查找成功的话，将返回一个long型的唯一的查找用的句柄。这个句柄将会在_findnext函数中被使用。失败返回-1.

参数： filespec：标明文件的字符串，可支持通配符。比如：*.c，则表示当前文件夹下的所有后缀为C的文件。

fileinfo ：这里就是用来存放文件信息的结构体的指针。这个结构体必须在调用此函数前声明，不过不用初始化，只要分配了内存空间就可以了。函数成功后，函数会把找到的文件的信息放入这个结构体所分配的内存空间中。

> **int _findnext( long handle, struct _finddata_t \*fileinfo );**

返回值：若成功返回0，否则返回-1。

参数： handle：即由_findfirst函数返回回来的句柄。

fileinfo：文件信息结构体的指针。找到文件后，函数将该文件信息放入此结构体中。

> **int _findclose( long handle );**

返回值：成功返回0，失败返回-1。

参数： handle ：_findfirst函数返回回来的句柄。该结构体和搭配的函数使用的逻辑就是先用_findfirst查找第一个文件，若成功则用返回的句柄，调用_findnext函数查找其他的文件，当查找完毕后用，用_findclose函数结束查找。

### 其他一些函数介绍

string：

c_str()提供了这样一种方法，它返回const char*类型(可读不可改)的指向字符数组的指针

assign()常用在给string类变量赋值. 如str2.assign(str1);即用str1给str2赋值.

string.append()添加文本使用append()添

加文本常用方法:直接添加另一个完整的字符串:如str1.append(str2);



### 具体实现

``` C++
#include <string>
#include <io.h>
#include <vector>
#include <iostream>
#include <fstream>
using namespace std;

/************************************************************************/
/*  获取文件夹下所有文件名
	输入：
		path    :   文件夹路径，例如path="E:\\Test\\image"
		exd     :   需要获取的文件名后缀，如exd = "jpg";如果希望获取所有
					文件名, exd = ""
	输出：
		files   :   获取的文件名列表
*/
/************************************************************************/
void getFiles(string path, string exd, vector<string>& files)
{
	//文件句柄
	intptr_t   hFile = 0;
	//文件信息
	struct _finddata_t fileinfo;
	string pathName, exdName;

	if (0 != strcmp(exd.c_str(), ""))
	{
		exdName = "\\*." + exd;
	}
	else
	{
		exdName = "\\*";
	}
	
	//判定该文件夹是否有文件
	if ((hFile = _findfirst(pathName.assign(path).append("\\*").c_str(), &fileinfo)) != -1)
	{
		do
		{
			//如果是文件夹
			if((fileinfo.attrib &  _A_SUBDIR))
			{
				if (strcmp(fileinfo.name, ".") != 0 && strcmp(fileinfo.name, "..") != 0)
				{
					getFiles(pathName.assign(path).append("\\").append(fileinfo.name), exd, files);
					std::cout << pathName << std::endl;
				}
					
			}
		//如果是文件
			else
			{
				string str = fileinfo.name;
				string suffixStr = str.substr(str.find_last_of('.') + 1);//获取文件的后缀
				//std::cout << suffixStr << std::endl;
				if(suffixStr == exd)
					files.push_back(pathName.assign(path).append("\\").append(fileinfo.name));
			}
		} while (_findnext(hFile, &fileinfo) == 0);
		_findclose(hFile);
	}
}
int main(int argc, char* argv[])
{
	vector<string> files;
	string folder_path = "E:\\Test\\image";
	string postfix = "dat";
	//获取该路径下的所有jpg文件
	getFiles(folder_path, postfix, files);
	for (auto file : files)
	{
		std::cout << file << std::endl;
	}
        //如果需要保存的话
	/*
	ofstream outf;
	outf.open("fileName.txt");
	int size = files.size();
	for (int i = 0; i < size; i++)
	{
		outf << files[i].c_str() << endl;
		//cout<<files[i].c_str()<<endl;
	}
	*/
	return 0;
}
```



