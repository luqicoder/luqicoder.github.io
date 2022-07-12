# 利用quicker快速上传图片到图床(阿里云oss)

通过quicker设置一组动作，实现快速截取图片并上传到阿里云的oss。

<!--more-->

## 正文

&emsp;&emsp;一直都想解决如何快速上传图片到图床问题。以前每次写博客的时候，需要插入图片的时都是先把图片截取下来，保存到本地，然后通过网页的方式上传到阿里云，最后再获取上传的图片链接。通过这一系列的步骤，其实让人感觉挺麻烦的，所以自己都尽量避免在博客中插入图片。
&emsp;&emsp;quicker是不久前才接触到，当时是为了解决论文翻译的问题（这里强烈安利一波沙拉查词+quicker的方式，真的是看英文文献的神器）。quicker有点像脚本精灵的意思，通过组合一系列动作，以达到快速完成某个任务的目的。使用了一段时间后，就有了开发这个阿里云oss快速上传动作的想法，下面是动作的效果图：

![Alt text](https://bucket-coder67.oss-cn-shenzhen.aliyuncs.com/gif/aliyun_oss_gif.gif "演示效果" )

### 环境要求

python3(同时需要安装**oss2**和**sys**这两个库，python需要设置在环境变量中)
参考安装命令：

```python
pip install oss2
pip install sys
```

### 使用说明

使用之前请在阿里云设置好AccessKeyId，AccessKeySecret；同时创建自己的Bucket。第一次使用该动作需要配置如下参数：

* AccessKeyId
* AccessKeySecret
* Bucket的Endpoint
* Bucket名字
* Bucket的根文件夹（即文件需要保存在Bucket的哪个文件夹下面）
* 选择本地文件夹用于存放截取的图片(例如下图中**quicker_screen_shot**)
  
  ![Alt text](https://bucket-coder67.oss-cn-shenzhen.aliyuncs.com/hexo/20200605/20200605002558.jpg "本地quicker_screen_shot文件夹")

  在上图中，以一串时间当做文件名的图片为我们自己截取的图片，所有文件均上传至阿里云oss(如果本地不需要可自行删除)；

Note：**aliyun_oss.py**为自动生成的脚本文件，请一定**不要删除**。

## ToDo

- [ ] 实现本地文件上传
- [ ] 实现网络链接图片上传

