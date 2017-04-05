---
title: LaTeX修改数学公式字体
date: 2017-04-04 00:00:22
tags: LaTeX
description: "LaTeX 修改数学公式字体."
---

## 几句废话

$\LaTeX$ 的公式环境总的来说不难看，但同一个字体看的次数多了，也难免审美疲劳。加上有些环境下编译的时候字体也比较难看，所以了解一下怎么修改公式的字体就是必须的了。

## 修改方法

$\LaTeX$ 中修改公式字体的方法就是把对应的包加上就可以了，我用过的不错的字体有以下几种：

### Mathptmx

**设置方法**如下：

``` 
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage{mathptmx}
\usepackage{bm} 
```

效果如下：

![image_1bcq9rkcm1e1e18v3s5aiu337l9.png-21.5kB][1]

### Fourier

别把这个和傅里叶联系起来哈……虽然好像确实是傅里叶。。

**设置方法**如下：

```
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage{fourier}
\usepackage{bm} 
```

效果如下：

![image_1bcq9v8ll3g192i8n7kvi1c9jm.png-20.9kB][2]

### Eulervm

**设置方法**如下：

```
\usepackage{amsmath}
\usepackage{amssymb}
\usepackage{ccfonts}
\usepackage[euler-digits]{eulervm}
\usepackage{bm} 
```

效果如下：

![image_1bcqa813i155614ba12h11hk0n2u13.png-23kB][3]

## 参考资料

- [LaTeX技巧86：常用的数学公式字体（含代码）一 ][4]


  [1]: http://static.zybuluo.com/lzcwr/byvnq0i9n5z08k52qby9jf2g/image_1bcq9rkcm1e1e18v3s5aiu337l9.png
  [2]: http://static.zybuluo.com/lzcwr/byn049o08zgpwt3i7qwqr5eo/image_1bcq9v8ll3g192i8n7kvi1c9jm.png
  [3]: http://static.zybuluo.com/lzcwr/wnn2y9xvridffmxvepsfauqt/image_1bcqa813i155614ba12h11hk0n2u13.png
  [4]: http://blog.sina.com.cn/s/blog_5e16f1770100g593.html