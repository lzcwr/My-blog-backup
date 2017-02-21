---
title: Mathematica Inpaint函数——图像复原与处理
date: 2017-02-16 20:30:17
tags: Mathematica
description: "对Mathematica Inpaint函数功能及用法的普及性介绍."
---

## 函数介绍

官方文档中对该函数的翻译是“润饰”：

```
Inpaint[image, region]
    润饰 region 中非零元素的 image 部分
```

Inpaint函数在使用时，需要两个参数，分别是*image*和*region*。一般来说，*image*就是需要处理的原始图像，而*region*更像是一个mask。所以Inpaint一般需要接受两个大小相同的图像，也总是会返回和image有着相同大小和类型的图片。

---

Inpaint函数有接受Method选项，并给出如下方法：

|方法|含义|
|:--:|:--:|
|"Diffusion"|迭代各向同性扩散方法|
|"TotalVariation"|迭代全变差方法|
|"FastMarching"|快速行进方法|
|"NavierStokes"|Navier-Stokes 方法|
|"TextureSynthesis"|利用随机采样的最佳拟合纹理合成方法|

默认情况下，使用TextureSynthesis方法。

---

设置 Method->{"TotalVariation", *subopt*} 下，可以指定下列子选项：

| 子操作 | 值 | 含义|
|:-----:|:-----:|:---:|
|"NoiseModel"|"Gaussian"|"Gaussian"、"Laplacian"或"Poisson"|
|"Regularization"|Automatic|正则化参数|


---

设置 Method->{"TextureSynthesis", *subopt*} 下，可以指定下列子选项：

|子操作|值|含义|
|:---:|:---:|:---:|
|Masking|Automatic|为二元掩模，可指定用于求出最佳拟合纹理的像素
|"MaxSamples"|300|用于求出最佳拟合纹理的样本最大数量|
|"NeighborCount"|30 |用于纹理比较的邻近像素的数目


---
Inpaint 接受 MaxIterations 选项，指定使用迭代方法执行迭代的最大数. 默认设置是 MaxIterations->100。关于参数，本文不再详谈。

## 两个应用

Inpaint函数有着强大的功能，在图形处理中有着不可思议的作用，以下依据[官方文档][1]介绍Inpaint的两个较为直接的应用。

### 修复图像损坏的部分

用合适的Mask图片传入Inpaint可以修复图像的损坏部分：

![image_1b95789ipkjg1gudqgq9mqrmg57.png-182.4kB][2]

在处理中也可以使用其他的Method选项：

![image_1b9579v3t1mvv1nc0163e18ps27q5k.png-83.3kB][3]

### 去除图片中较大的对象

通过调整Mask图片的形状和黑白区域分配，可以去除图片中较大的对象。以下是官方文档中的两个例子：

![image_1b957fb3hjgqv46l3q1af9l761.png-55.6kB][4]
![image_1b957g7671akai2lt7hsu358n6e.png-99.4kB][5]

 

## Mask图片的生成技巧

从以上例子中可以看出，关于图像的修复和改动，最关键最核心的就是函数中的region参数，也就是Mask图片的生成。以下讨论两种生成Mask图片的方法：


### 掩模工具

利用“去除较大对象”的功能以及`Mathematica`自带的掩模工具，可以去除图片上的水印。
例如下面这张图片，也就是我的头像。来源是[百度百科-正一百二十胞体][6]。但图片左下角带有百度百科的水印：

![image_1b93g3b6r1ac3qugnj33q4pgj1f.png-75kB][7]

利用Inpaint函数可以很好地处理这件事情，以下是处理的方法：


 - 先将图片导入`Mathematica`：![mma1-1.jpg-20kB][8]
 - 然后进行掩模，即生成一个黑白的图片，其中黑色的部分保留，白色的部分去除：![mma1-2.jpg-18.5kB][9]
 - 掩模完成后就可以直接调用Inpaint函数将左下角的水印去掉了（`Mathematica`可以直接把图片作为参数，非常便捷）：![mma1-3.jpg-9.7kB][10]
 - 然后将图像导出，即可得到一个没有水印的图片（如下图所示）。


![image_1b93giupt1321lqv26p1b2r183p3j.png-75.7kB][11]


在很多情况下，用掩模工具生成Mask图片作为region参数对图像进行处理都是非常方便的。
 
### 利用颜色的值自动从原图生成Mask
 
还有一种方法能够直接从原图中生成Mask图片，以下是官方文档中非常具有启发性的一个例子：

![image_1b958k6rv1mg1ai41lafg3abd26r.png-104.6kB][12]

在这个例子中，非常完美地地去掉了图片右下的日期标记，而且在命令中没有直接出现Mask图片，而是用了一行命令来自动生成：

```
Dilation[Binarize[ColorSeparate[img][[1]], 0.93], 1]
```

其含义是：用ColorSeperate函数将图片分为RGB三层，然后将Red层像素值大于255×0.93的设为白色，其余为黑色，然后用二值化函数Binarize来生成黑白图片作为region参数。其中的Dilation函数是一个处理函数，本文不多赘述。


由这个例子，我们可以产生一种想法：
用图像处理的软件很简单地将图片中不想要的部分染色（染成一个图片当中没有的颜色），然后用上面的方法生成一个region参数，再对原图进行处理。此时便不再需要掩模：

 - 比如这样一张照片：
![image_1b94pcrduqe51l2h1k4c12hq10g240.png-242.1kB][13]
 - 用工具把不想要的部分染色，作为Mask
![image_1b94pkcvg9q7705s4r1fdtupo4d.png-199.2kB][14] 
 - 运行代码可得如下结果，效果 <del>不咋地</del> 很好，但是确实能去掉该部分。（原因是像素值筛选范围的设定出现了问题，不过我比较懒，没有进行更好的调整）
![image_1b94qh78nbpvmfq6tm1e471d5h4q.png-234.6kB][15]

原理和官方文献中的类似，于是处理这样的问题**只需要一行代码**：

```
Inpaint[#1,ImageResize[Binarize[#2,#=={1,0,0}&], ImageDimensions@#1]]&[img,mask]
```


## 纹理和画作分析

如果说刚刚的功能看起来也比较平常的话，那么根据一幅画来创作一幅风格相近的画，或是分析图片中的纹理，总应该是看起来很高端很厉害的功能了。以下是官方文档中的例子：

 - 纹理合成：


 ![image_1b95a50op1m0h14pbs74fjdp6978.png-125.8kB][16]


 - 画作分析：


 ![image_1b95auqqggfh1lic1f5k1qsq16tc7l.png-267.5kB][17]

 


有关`Mathematica`的其他资料请自行查阅。本文只做普及，不做详解。
 

 

  [1]: http://reference.wolfram.com/language/ref/Inpaint.html
  [2]: http://static.zybuluo.com/lzcwr/acvr1e10vda50lld9excuywm/image_1b95789ipkjg1gudqgq9mqrmg57.png
  [3]: http://static.zybuluo.com/lzcwr/xzw3f225yjm0ggjr98sfi3sr/image_1b9579v3t1mvv1nc0163e18ps27q5k.png
  [4]: http://static.zybuluo.com/lzcwr/3qjuejedy1c4s4ztljrc40h7/image_1b957fb3hjgqv46l3q1af9l761.png
  [5]: http://static.zybuluo.com/lzcwr/xbwvvxwxmytb0o8rl9uyc159/image_1b957g7671akai2lt7hsu358n6e.png
  [6]: http://baike.baidu.com/link?url=KT1OSCvJNCAtnTD8lciSq6sfiH8Zd2hDvqy93GIykGWVhQ2sDAVivz4fyu41sHA-vu1ZWB_LDp61jTpd6GodvrEWPS9Fndw9lrQ_ek6-0xDlHnj7v4LuH5S9TmCUUIHnfPhhmYJogwklspdtxiq-HeAaWAdvJ2MZDjM3Y8v8klW
  [7]: http://static.zybuluo.com/lzcwr/xh1jopg1uuaq5f5k2uqoamvn/image_1b93g3b6r1ac3qugnj33q4pgj1f.png
  [8]: http://static.zybuluo.com/lzcwr/s4c1yxtq4yrk05qzuqr0wcnr/mma1-1.jpg
  [9]: http://static.zybuluo.com/lzcwr/bqzwzw262qdhi0ocq4lco285/mma1-2.jpg
  [10]: http://static.zybuluo.com/lzcwr/hunbysi0g3gojegdgdlmmuay/mma1-3.jpg
  [11]: http://static.zybuluo.com/lzcwr/05x06nkjuw8oztloswld5zgs/image_1b93giupt1321lqv26p1b2r183p3j.png
  [12]: http://static.zybuluo.com/lzcwr/qfsl33sfd2l7a44mv4cdnz8a/image_1b958k6rv1mg1ai41lafg3abd26r.png
  [13]: http://static.zybuluo.com/lzcwr/o2ihbg3nlnrfe0qtvvkza3u9/image_1b94pcrduqe51l2h1k4c12hq10g240.png
  [14]: http://static.zybuluo.com/lzcwr/3tin320gpf8dhifgxvor4rf9/image_1b94pkcvg9q7705s4r1fdtupo4d.png
  [15]: http://static.zybuluo.com/lzcwr/g1t9y9iqf73nf3vnxwcm08x9/image_1b94qh78nbpvmfq6tm1e471d5h4q.png
  [16]: http://static.zybuluo.com/lzcwr/1od15epr9dwdghtqpttbo7jz/image_1b95a50op1m0h14pbs74fjdp6978.png
  [17]: http://static.zybuluo.com/lzcwr/00ngpy90vzmlfb2zgn2jw2l0/image_1b95auqqggfh1lic1f5k1qsq16tc7l.png