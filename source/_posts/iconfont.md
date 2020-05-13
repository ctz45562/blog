---
title: Iconfont:一款功能强大的图标库
date: 2019-09-18 09:48:12
categories: 颓
tags:
	- blog
keywords: iconfont
description: 有了Iconfont，妈妈再也不用担心我找不到想要的图标啦！
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/iconfont.jpg
mathjax: false
---

有了Iconfont，妈妈再也不用担心我找不到想要的图标啦！

<!--more-->

# 前言

搭blog的时候图标一直在用[Font Awesome库](http://www.fontawesome.com.cn/faicons/)。然而图标只有**不到700**个。

如果科学上网去[正规Font Awesome库](https://fontawesome.com/)的话，图标也只有**7000多个**，而且大部分都是付费的。

偶然看到[Iconfont库](https://www.iconfont.cn/home/index?spm=a313x.7781069.1998910419.2)，里面图标有**60多万**个，而且还有彩色的！

于是有了这篇教程。

# 添加

可以按[这个教程](https://www.jianshu.com/p/8f14411824b2)下到本地，不过很麻烦不推荐。

直接用阿里云外链调用即可。

首先来到[Iconfont官网](https://www.iconfont.cn/home/index?spm=a313x.7781069.1998910419.2)，点右上角登录（`github`登录即可）。

点击首栏->图标管理->我的项目。

![](\images\iconfont-1.png)

点击右上角紫色的新建项目。

![](\images\iconfont-2.png)

`项目名称`、`FontClass/Symbol前缀`和`Font Family`随便填，后两项引用的时候要用。

![](\images\iconfont-3.png)

搜索想要的图标。

![](\images\iconfont-4.png)

点击图标上的“添加入库”按钮。

![](\images\iconfont-5.png)

如果还有其他需要的，可以继续添加。

点击右上角的购物车，弹出的侧边栏里点击`添加至项目`，选择刚才创建的项目。

![](\images\iconfont-6.png)

回到刚才的项目，在`Font class`中生成css外链。

![](\images\iconfont-7.png)

复制css外链，在博客页面的`<head>`元素中引用：

![](\images\iconfont-8.png)

# 使用

通过`<i>`标签引用。

格式：

``` html
<i class="A B-C"></i>
```

`A`为`FontClass/Symbol前缀`，`B`为`Font Family`，`C`为图标名称。

示例：

```html
<i class="ctz ctz-snow"></i>
```

<i class="ctz ctz-snow"></i>

# 优点

- 图标数量大，样式美观
- 图标名多为中文名，便于搜索
- 阿里云外链，速度快
- 可以修改图标，甚至自制图标上传