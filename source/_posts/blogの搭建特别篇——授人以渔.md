---
title: blogの搭建之sakura特别篇——授人以渔
date: 2020-02-05 08:54:59
categories: 颓
tags:
    - blog
keywords: blog教程
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.6/blogddjzsakuratbpsryy.jpg
mathjax: true
---

之前有一篇[授人以鱼](/2019/06/04/blogの搭建之sakura)的教程，后来看到有人留言希望我授人以渔，再加上我本来就有这个想法，以此文作为退役前给后人的一点贡献吧。

<!--more-->

---

# 前言

我是作为一个OIer沉迷于搭blog的，没有过多的时间和精力系统地学习前端知识。本文旨在帮助和我一样不能专门学习搭blog的人（尤其是OIer）快速上手，能够对blog简单地个性化，所以里面都是我半吊子的知识，请技术大佬勿喷。

一点声明：我基本所有的精力都投入到sakura上了，所以涉及主题方面的也只能针对sakura来说，其他主题的还得读者仿照着去研究。

---

# 基础

## 编辑器

功能全面、界面美观的VScode是不错的选择。

不过VScode在机房的电脑略卡，所以再说一下我早些时候用的Notepad++。几乎不占用资源，打开上百个文件内存不到30M，一些基本的功能也挺全的。

## 三大语言

html、css、javascript。

W3school、菜鸟教程等网站对这些语言的教程很详细。

这三种语言不要求完全掌握，如果搭blog只是你的一种兴趣，不要让它占用太多时间。

所以：

- 学会照葫芦画瓢，能模仿着原有的语句写出类似功能的语句，下面的东西，很多都是靠模仿学会的
- 不要死记硬背函数、语法，忘了或者不会不要紧，善用搜索引擎

### html：网页的骨架

乍一看画风很鬼畜，实际上很好上手。

要能够大致看懂一个html文件的构造并掌握创造一些常用元素如`<div>,<p>,<span>,<hr>`并添加属性。

为了更好地看懂hexo的html文件，了解了基础语法后，要掌握一种进阶的语法。

形如`<% ... %>`，`<%= ... %>`，`<%- ... %>`（也有主题是`｛% ... %｝`），后面会有说到。

### css：网页的皮肤

洛谷4.0之前应该还有一些人会css的，通过插件`stylus`/`stylish`基本就能掌握。

当然我还是推荐找专门的教程学一学。会给某一个或某一类元素添加一些基本的属性如`background,color,font-size,height`。至于其他你想要的效果，善用搜索引擎。

### javascript：网页的血肉

类似于`c++`，简单了解js的语法就能上手了。

但js相比于前两者更nb，也更难写，很多时候还是靠模仿。

只需要会最基础的函数`getElement`家族、`createElement`、`setInterval`等，以及最基础的语法如`var`的用法、循环遍历、定义函数。

其他的，搜索引擎帮你搞定。

## F12大法

在网页中摁下F12，打开开发者工具。

开发者工具用处非常大，一定要掌握。

不同浏览器外观不同，但功能是差不多的。这里以firefox为例，其他浏览器的差异请读者研究吧。

### 查看器

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/1.png)

直接查看和修改内部html构造和css样式。

右键对指定元素操作。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/2.png)

点击最左上角的按钮（红色圈出来的），可以用鼠标定位元素。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/3.png)

搜索元素需要用css语法。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/4.png)

右边第二栏可以查看所有与指定元素有关的css样式并修改。修改已有规则或者添加新规则都可以。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/5.png)

点击规则左侧的对勾禁用/启用。

在右上角还能看到来源（文件:行数）。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/6.png)

至于第三栏，我基本用不到，就不说了。

### 控制台

用于查看警告/错误信息和输出调试。

警告和错误都会在这显示，并在右侧标注文件。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/7.png)

而js里的`console.log()`函数就输出到了这里，可以用于调试。

下面的文本框用于输入js，定义变量、调用函数之类的。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/8.png)

### 调试器

顾名思义，用于调试js。

可以看到网页引用的所有js并查看内容~~剽的一大利器~~

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/9.png)

单击行数设置断点，和`c++`一样调试。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/10.png)

右边那个“异常处暂停”也很有用，最好勾上。

### 网络

查看所有加载的文件以及加载效率，一般就是监测有没有加载奇♂怪的东西以及什么文件拖延了效率。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/11.png)

### 其他

后面几项平时没怎么用过，感兴趣的自行探索吧。

btw，右上角有一个“响应式设计模式”，可以把电脑变为手机浏览。

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/12.png)

用于改进移动端的体验。~~不过移动端的外观我已经完全抛弃了，所以丑的一批~~

甚至能进入一些只允许移动端访问的网页。

## FontAwesome&Iconfont

[FontAwesome](http://www.fontawesome.com.cn/faicons/)和[Iconfont](https://www.iconfont.cn/)是两个图标库，通过`<i>`元素加一些简单的图形，非常方便。

包括sakura在内，大部分主题都支持FontAwesome，但不支持Iconfont（准确来说，Iconfont的图标实在是太多，一般是从中选一些用，所以需要自己设置），[这里是添加Iconfont的教程](/2019/09/18/iconfont/)。

用法很简单，FontAwesome就在上面的链接里找到需要的图标，点进去，复制源码，放到html文件里适当的位置即可。

Iconfont用法教程里写了，和FontAwesome没啥区别。

更深入一点，可以看到图标的格式都是`<i class="fa fa-*" aria-hidden="true"></i>`（`aria-hidden="true"`可以省略），了解这个后有助于读懂html文件以及修改。

---

# hexoの构造

非常重要，摸清了hexo的目录结构就能对症下药了。

注：我没有提到的文件就是我不懂或者用不着。

## 站点目录结构

打开存放blog的文件夹，可以看到以下内容：

### .deploy_git

`hexo d`生成，`hexo cl`删除。

这是`git`上传用的文件夹，没啥用，不要管它。

### public

`hexo g`/`hexo d`生成，`hexo cl`删除。

`hexo g`会根据你的`/source`文件夹和主题生成静态的网页，即将要上传到`github`仓库，`/public`就是存放这些的。一般也没用。

### node_modules

用于存放插件。

可以修改，但是内容都比较深奥看不透，看个人水平。

### scaffolds

存放模板。你可以看到里面有几个`markdown`文件。当执行`hexo new [title]`或`hexo new page [title]`时会根据模板创建文件。

`post.md`是文章模板，`page.md`是页面模板，根据个人习惯改一下写东西更方便。

### source,_config.yml

这两个不用多说了吧。

把文件放在`source`里，可以直接通过链接`/path`（`path`为在`source`下的路径名）引用文件。例如，在`source`文件夹下创建文件夹`images`，里面放上文件`1.jpg`，就可以用路径`/images/1.jpg`引用图片了。甚至可以用`博客网址/images/1.jpg`在博客外的地方引用。

### package.json

记录所有已安装的插件和版本信息。

有的主题会提供整个博客文件夹（`sakura`就是），如果出了bug，可以对比一下`package.json`查看是否是缺少插件或插件版本不够导致的。

## 主题目录结构

不同主题的目录结构是有差异的，但是思路是一样的。这里就以sakura主题为例讲解。

再一个，不同主题的html文件后缀名可能不同，例如sakura是`.ejs`，next是`.swig`，语法基本相同，以文件内容为依据。

打开`/themes/sakura`，你会看到：

### _config.yml

关于主题配置文件，也是很常见了。但不得不提一嘴：

主题配置文件中的东西，是用于html文件直接引用的，语法格式为`<%= theme.* %>`。

甚至能用`if`语句。举个栗子，如果在`_config.yml`里写下`testImg: /1.jpg`，在某个html文件里写下：

``` html
<% if (theme.testImg && theme.testImg.length){ %>
<img src="<%= theme.testImg %>" alt="">
<% } %>
```

这段话的意思就是，如果在`_config.yml`里有`testImg`的定义且不为空的话，就显示`testImg`代表的图片。

如果你回去看看我给归档页面添加配图的教程，你会发现我就是用这种方式添加的。这个还是很实用的，多模仿。

### languages

不多说了，写博客都能用到。

### layout

划重点。要考的。

这里存放了主题的骨架，也就是所有html文件，后缀名都是`.ejs`。一个一个说。

#### 板子

一些页面或重要部件的模板。

里面你可能会看到这样一种语法：`<%- partial('...') %>`。例如在`layout.ejs`里就有`<%- partial('_partial/head') %>`，它的意思是把`_partial/head.ejs`里的内容原封不动地搬过来。

- `layout.ejs`：最重要的html文件。**几乎所有**页面和文章都以此为基础。如果有什么东西想对所有页面生效（比如看板娘），放在这里就行
- `category.ejs`：某一分类下所有文章（[例子](/categories/颓/)）
- `tag.ejs`：某一标签下的所有文章（[例子](/tags/blog/)）

`_partial`文件夹下：

- `head.ejs`：专门写`<head>`标签的内容。有了html基础就知道里面该写什么了
- `footer.ejs`：专门写`<footer>`标签的内容。同上
- `header.ejs`：顶部菜单栏
- `mheader.ejs`：移动端下的顶部菜单栏

`_widget`文件夹下：

这里再说一种语法，`<%- post.* %>`和`<%- page.* %>`，这是在文章/页面`markdown`文件里的顶部，用`---`隔开的文章信息（称为`front-matter`），和上面主题配置文件说的一样用。

- `common-article.ejs`：文章的通用模板
- `common-page.ejs`：普通页面的通用模板
- `category-items.ejs`：某一标签/分类下单个文章。就是这个东西：

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/13.png)

- `index-items.ejs`：截图更明白：

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/14.png)

- `/search/insight.ejs`：搜索

#### 特殊页面

这一类特殊页面仅靠`markdown`是不够的，都会有一个专门的html文件。

- `archive.ejs`：归档
- `bangumi.ejs`：番剧
- `donate.ejs`：赞赏
- `index.ejs`：主页
- `links.ejs`：友链

#### 零件

`_partial`文件夹下：

- `aplayer.ejs`：音乐播放器
- `comment.ejs`：评论
- `headertop.ejs`：这个：

![](https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.5/blogcourse/15.jpg)

- `startdash.ejs`：顾名思义

### source

划重点，也要考的。

和前面的`/source`一样，主题文件夹下的`source`同样可以用来引用文件，格式完全一致。

普及一个常识：如果一个css/js文件后缀名前面有个`min`或`mini`，那是说它被压缩了。压缩文件是为了缩小文件体积，加快传输效率，但是也失去了代码可读性。我在教程最后给出的工具里有压缩和解压css/js的。但是js的压缩一般会把变量名和函数名压缩，这是不可逆的。。。注意一下。

#### css

- `style.css`：最重要的css文件。绝大多数样式规则都在这里。要修改或者添加就到这儿。其它的稍作了解即可，大部分看名字就知道是干啥的了
- `APlayer.min.css`：音乐播放器
- `bangumi.css`：番剧页面
- `donate.css`：赞赏页面
- `font.css`：iconfont相关。不要动它
- `insight.styl`：搜索相关，不过看起来有些奇怪。这是另一种css，照葫芦画瓢造它就完了
- `jquery.fancybox.min.css`：fancybox相关
- `lib.min.css`：动态图标相关。不要动它
- `sharejs.css`：转载相关
- `zoom.css`：zoom相关

#### js

- `sakura-app.js`：最重要的js文件。大部分关键函数都在这里。和`style.css`一样，要修改或者添加就到这儿。其它的稍作了解即可，大部分看名字就知道是干啥的了
- `APlayer.min.js`：音乐播放器
- `busuanzi.pure.mini.js`：busuanzi访客量计数器
- `InsightSearch.js,search.js`：搜索
- `jquery.fancybox.min.js`：fancybox
- `lib.min.js`：很杂的基础函数，不要动它
- `valine.min.js`：valine评论
- `zoom.min.js`：zoom

#### fonts

用于存放fontawesome和自带的iconfont，不要管

---

# 扯两句

## 我是怎么美化blog的

**1.创新**

突然灵光一现，有了新点子。

刚加上主题工具的时候，“自由切换背景和字体很炫啊，那是不是还可以更进一步呢？”于是「主题工具-特效」诞生了。

当然这玩意的代码都得自己写，很费时间。本来我还想再加个「主题工具-鼠标」的，太累了写不动了。。。

**2.修改**

经常浏览自己的blog，看到不顺眼的就改掉它。

只要熟悉了hexo构造就很轻松了。

**3.剽**

~~这才是主要手段~~

没事多去逛逛别人的blog，或者看看hexo的其他主题和教程，发现很棒的东西就搬过来。

有教程的还好说，没有的只能靠F12生剽。但生剽不是`Ctrl-C,Ctrl-V`，考虑到兼容性，一般是了解了原理后自己写出来。所以这活儿其实很累。

比如我现在的标签样式，从spfk主题搬过来的，但是因为html构造不同，很多样式都要修改，再加上随机颜色和修复bug，基本都相当于自己写了。

## 搭blog很累

想极致地美化blog确实很累，有时候比OI都难，完全靠兴趣支撑。

绝大部分的颓废时间，别人在水知乎水群，而我不是在找新点子，就是对着源码改来改去。周围有的人不太理解这有啥有意思的，我其实也不明白，大概只是享受blog称我心意的成就感吧。

如果你真的不想费劲还想美化blog的话，我只能说，别自己搞了，还是大佬的教程适合你。

## 注意性能

尽管我现在能力不足，blog的效率并不高，但还是要说一说。

**速度对访问体验的影响绝对比美观大。**

如果第一次进入都要加载超过10s甚至失败，那很可能就不会有第二次访问了。

进行一项改动时，要考虑对性能的影响。

没事就观察一下blog的速度并尽可能优化。注意用`Ctrl+F5`刷新缓存，能观察到真实的速度。

一方面是网络速度。很多人都是用`github`自带的域名或者买个廉价/免费的域名，网站加载效率不高，所以尽量不要使用`/source`文件夹引用，速度非常慢（大概10k/s），特别对于sakura，图片非常多。目前我在用jsdelivr，加速所有较大的图片、js、css。

另一方面是cpu和内存的占用。一般是花里胡哨的功能导致的，比如看板娘、背景特效。加入某项东西的时候，要考虑到对性能的影响。某人的blog，虽然是next主题，但是cpu占用达到50%，内存占用超过1G！机房里的电脑性能本来就一般，很多 人访问他的blog差点死机。u1s1，这种blog也不会有什么回头客了吧。

## 一些工具

[有很多动画特效且提供源码](http://www.jq22.com/daima3)

[测速网站](https://ping.chinaz.com/)

[又一个测速网站（你需要一架梯子）](https://developers.google.com/speed/pagespeed/insights/)

[各种工具（js工具和css工具很实用）](https://tool.lu/)

压缩系列：

[高效无损图片压缩](https://tinypng.com/)

[自定义压缩比例，损失清晰度换取压缩度](http://www.bejson.com/ui/compress_img/)

[gif压缩](https://docsmall.com/gif-compress)

---

# 后记

断断续续写完了。算是退役前留下的最后的痕迹吧。

我也不适合教人，语言混乱，还可能漏洞百出，所以欢迎大佬们指出错误。

