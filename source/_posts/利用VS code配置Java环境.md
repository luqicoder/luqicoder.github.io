---
title: 利用VS code配置Java环境
date: 2020-03-27 11:54:57
categories: 
- Java
tags:
- 环境配置 
typora-root-url: ../
---

### 前言

*最近在看HeadFirst设计模式，其中代码都是Java写的。虽然大学时候学过一部分Java，但是基本没怎么用，忘得差不多了。然后就从[廖雪峰Java教程](https://www.liaoxuefeng.com/wiki/1252599548343744)看了一部分Java基础概念。开发工具我这里选择的是vscode。*

 <!-- more -->

### 1.安装VS code

​	从https://code.visualstudio.com/下载vscode。

### 2.安装Java extension Pack插件

<img src="/images/利用VS code配置Java环境 (7).png" alt="头像" style="zoom: 50%;" />

### 3.配置jdk环境

​	Java extension Pack插件安装完毕后会自动跳转到Configure java Runtimes页面，并检测当前环境是否存在JDK。如果没有存在的话，可以看到下方提供有下载按钮，进行jdk安装。

​	打开下载的安装程序，点击下一步，勾选设置JAVA_HOME_VARIABLE。

<img src="/images/利用VS code配置Java环境 (8).png" alt="头像" style="zoom: 40%;" />

<img src="/images/利用VS code配置Java环境 (1).png" alt="头像" style="zoom: 40%;" />

<img src="/images/利用VS code配置Java环境 (2).png" alt="头像" style="zoom:40%;" />

### 4.运行java程序

​	首先需要重启vscode，打开一个新的文件夹。

<img src="/images/利用VS code配置Java环境 (3).png" alt="头像" style="zoom:33%;" />

在文件夹中新建一个Hello.java文件。

<img src="/images/利用VS code配置Java环境 (4).png" alt="利用VS code配置Java环境 (6)" style="zoom:50%;" />

<img src="/images/利用VS code配置Java环境 (5).png" alt="头像" style="zoom:50%;" />

​	输入以下代码：

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

​	然后有右键运行，输出结果。

<img src="/images/利用VS code配置Java环境 (6).png" alt="头像" style="zoom:33%;" />

### 参考：

https://code.visualstudio.com/docs/java/java-tutorial