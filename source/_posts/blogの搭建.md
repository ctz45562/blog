---
title: blogの搭建之next
date: 2019-03-21 19:54:59
categories: 颓
tags:
	- blog
mathjax: true
keywords: next
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/blogddjznext.jpg
description: 可以说这是份指南，也算是如果某天blog炸了后的重构指导书。（其实我只是今晚想颓）
---

可以说这是份指南，也算是如果某天blog炸了后的重构指导书。~~（其实我只是今晚想颓）~~

<!--more-->

---

# 前言

首先来一句：next吼啊！（破音

不得不说next主题提供的配置还是非常全面的，几乎800行的配置文件看着就爽啊。本文记录我对next-Gemini主题的修改。其中大部分也适用于其他`hexo`主题。

目前我已换成了sakura主题，本文不再更新。sakura修改内容全部写在[blogの搭建之sakura](/2019/06/04/blogの搭建之sakura/)里。

---

# 常见配置

什么Latex、鼠标点击特效、动态背景、访客计数器等等网上关于next的全是，就不再说了。主要说一些不好找的。

---

# live2d看板娘

## 普通的

`git bash`中执行：

```bash
npm install hexo-helper-live2d --save
```

在站点配置文件中添加：

```
live2d:
  enable: true
  scriptFrom: local
  pluginRootPath: live2dw/
  pluginJsPath: lib/
  pluginModelPath: assets/
  tagMode: false
  debug: false
  model:
    use: live2d-widget-model-wanko # 模型名称
  display:
    position: right # 位置
    width: 285 # 宽
    height: 570 # 高
    hOffset: -20 # 水平向左偏移量
    vOffset: -50 # 垂直向上偏移量
  mobile:
    show: true
  react:
    opacity: 1 # 透明度
```

更多的设置可以参考[官方说明](https://github.com/EYHN/hexo-helper-live2d/blob/master/README.zh-CN.md)。

更换模型：

[这个地方](https://blog.csdn.net/wang_123_zy/article/details/87181892#_1)里有模型名称和预览。找到想要的（以`live2d-widget-model-z16`为例），执行：

```bash
npm install live2d-widget-model-z16 --save
```

在上面加的文本中，模型名称一栏改为`live2d-widget-model-z16`。

## 高级的

### 添加

可以换装、说话、换看板娘、玩游戏、拍照。

首先把[这个仓库](https://github.com/stevenjoezhang/live2d-widget)的压缩包下载，解压到一个文件夹里。或者在git bash中克隆到一个文件夹里：

```bash
git clone https://github.com/stevenjoezhang/live2d-widget
```

然后整个文件夹扔进`\themes\next\source`​里，记住这个文件夹的名字（名字可以改）。

打开文件夹里的$autoload.js$，把第一行的：

```javascript
const live2d_path = "...";
```

$...$改成

```javascript
/你刚才记的名字/
```

打开`\themes\next\layout\_layout.swig`文件，把下面一句话扔到最后的`</body>`上面：

```html
  <script src="/你刚才记的名字/autoload.js"></script>
```

（不知道为啥网上的教程都把$autoload.js$写成了$autolload.js$，结果捣鼓了好久。。。）

如果玩过了普通版本，两个看板娘可能会冲突，把配置普通live2d时在主站配置文件添加的东西里enable底下的删掉（注释掉），然后把enable改为false。

### 调整

懂一点css、js、html可能会更好一些。。。（当然不会也行，蒟蒻就不会）

主要是在live2d文件夹里的$waifu.css$、$waifu-tips.js$、$waifu-tips.json$。

#### 位置

$waifu.css$文件中第一个大括号里：

```css
#waifu{
	...
}
```

里面的bottom改后面的数值可以调整上下位置；把left改为right能直接变到右边去（没试过，应该是可以的），后面的数值可以微调左右位置。

#### 大小

因为我没想调所以暂时不会（咕咕咕

#### 言论

$waifu-tips.json$和$waifu-tips.js$都有，前者好像更多。

就按里面的格式直接添加或删除就行了。

在$waifu-tips.js$里还能改某句话停留的时间。showmessage(文字,数字,数字)里第一个数字就是了（改炸了我不负责。。。）

### 一些小bug

1.把鼠标放上去和鼠标点击说话会冲突，我就直接把$waifu-tips.json$里鼠标放上去那一段删掉，里面的话都移到点击事件里了。

2.第一个看板娘Pio眼睛偶尔会短暂消失。。。好像是开发者的bug吧。。。

> 参考：
>
> https://www.jianshu.com/p/89440678ee3c
>
> https://blog.csdn.net/dataiyangu/article/details/83021854?utm_source=blogxgwz3
>
> https://imjad.cn/archives/lab/add-dynamic-poster-girl-with-live2d-to-your-blog-02

---

# 一言Hitokoto

先说一句我只会把一言放到侧栏上。。。

提供两种方法：

## Ⅰ

一直在找能不能添加随机一言在blog上，然而并没有找到什么教程，我也不会写js和html就搁置了很长时间。

后来偶然在某位大佬的博客里找到了一种比较粗糙的方法（好像仅适用next-Gemini主题？）：[附上链接](https://ouuan.github.io/hexo%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%8C%87%E5%8C%97/)

这里再感谢大佬，了却我一个心愿QwQ。

里面最后写了一言的添加方法，还是可行的。如果自定义格式的话可以到第一段添加的文字里魔改，font-size、color、font-family啥的应该看的懂吧。。。（改炸了我也不负责。。。）

不过还是有一些bug：

1.页面向下滚动时一言会突然消失，向上滚动又会突然出现。。。

2.一言过长时侧边栏遮不住，就会多出一块来。。。我是直接暴力延长侧边栏解决的。。。

因为现在换了挂个预览图：

![](\images\blog-2.png)

## Ⅱ

写完上面的第二天我又找到新方案了。。。上谷歌一搜就出来一篇。。。就是我现在的这种，主要是没啥bug，而且和下面个人信息合并了。

如果您用过上面那个想换的话，得先照着上面那篇再删掉。一定要注意别删多了。

[链接](https://willv.cn/2018/01/14/hitokoto/)

按里面说的配好hitokoto.js和\_layout.swig后，在\themes\next\layout\_macro\sidebar.swig里这段下面：

```html
    {% if theme.sidebar.onmobile %}
      <div id="sidebar-dimmer"></div>
    {% endif %}
    <div class="sidebar-inner">
```

添加：

```html
<div>
    <p class="hitokoto"></p>
    <p class="from"></p>
</div>
```

然后预览一下就会看到一言了！（如果想放到底下~~我猜的~~大概放到底下吧。。。可以试验一下）

当然这个很丑，需要自己改里面的格式（需要html语言，好像有大佬说这是swig语言。。。蒟蒻不太懂）。我费了2个小时现学html改成现在这样子。放上丑陋的配置：

```html
	  <div style="margin:1px auto;font-family:'微软雅黑';font-weight:bold; font-size: 19px; color: #100ae0">「 一言 」</div>
        <p class="hitokoto" style="font-family:'楷体'; text-indent:2em; margin-top:8px;margin-bottom:5px;text-align:left; font-size:17px;font-weight:bold"></p>
        <p class="from" style="font-size:15px;text-align:right; margin:0;color:grey"></p>
	  <hr style="height:2px; margin:10px auto;color:blue">
```

仅供参考，可以自己魔改。如果您是html大佬轻喷蒟蒻的半吊子html写法。。。

---

# 加载条调整

加载条的话在next主题配置文件中的pace改为true，下面的pace_theme可以选列出来的主题。

主要说一下个性化样式。（同机房大佬[CLT](https://challestend.github.io/)教的）

在`\themes\next\source\lib\pace`里找到你选的主题，改里面的`color`、`font-size`之类的。一个主题可能会有多个要改的地方注意一下。

---

# 如果你玩Stylus(会css)...

在`\themes\next\source\css\_custom\custom.styl​`文件中可以自由编辑css代码！

$update$：

这里写一个next的小bug：

（以下皆为闲扯）

很久之前wavwing想把blog换成绿色背景，我帮他写了个css。结果发现在侧栏个人信息出现前（它有动画效果延迟出现），下方会有白底，表示有强迫症，看着很难受：

![就像这样](\images\blog-3.png)

后来试了试其他背景同样会出现这个bug。于是尝试写css消掉它，不过我都不知道这个叫啥。。。

一开始傻了，拼手速在侧栏出现前F12抓取，然而时间太短抓不出来。。。

又试着写了关于侧栏`sidebar`模块的调整，对白底并没有影响。

很久之后的某天写了个`margin-left​`，发现侧栏和白底脱离了！

![](\images\blog-4.png)

这样F12就能随便抓取了。

于是有了解决方案：

在`custom.styl`里加上：

``` css
aside#sidebar.sidebar{
	background:#fff0;
}
```

成功解决！

---

# 评论

## 来必力

界面**非常美观**，登录方式多，管理快捷，功能全。之前是想一直用来必力的。

然而我没用它是因为它是外国的，加载比较慢甚至有时候加载不出来。。。（我的blog上放的东西太多很卡。。。）

当然还是稍微说一下：

网上还是有不少教程的，大概是申请个账号、安装一下、获取uid、扔到next主题配置文件里的livere_uid里就行了。还可以在来必力网站上设置邮件提醒、管理评论什么的。

## Valine

### 添加

访问速度快，支持markdown（不过不支持latex)，界面十分简洁，不需要登陆。

当然美观性还是比来必力差很多。。。

网上也是有不少教程的，在[leancloud](https://leancloud.cn/)上注册个账号照着配就行了。

如果用的github博客，评论里可能会提示

> code 403：api访问白名单拒绝bulabula（差不多这个意思）

（在localhost:4000预览时出现是正常的）可能是配置域名时（新建的应用->设置->安全中心->Web安全域名）只添加了http或https开头的域名，把另一个换行补上就好了。

### 邮件提醒

很麻烦。。。网上有教程。（[这个就不错](https://cloud.tencent.com/developer/article/1142500)）

这里说一下添加各种自定义环境变量这一步：

先[搞一下SMTP授权码](https://jingyan.baidu.com/article/7f41ecec3e8d35593d095c93.html)（我用的163邮箱，qq啥的从网上查一下吧应该有。。。）

添加的变量网上有好多版本，有的变量我都不知道干啥的。。。

如下图添加这些就能用：

![](\images\blog-1.png)

保存后要在云引擎->实例->设置图标->重启之后才会生效

管理评论：

在存储->数据->Comment里可以看到评论并删除

和看板娘的互动：

上面说的高级的看板娘会在Valine评论里时有互动，和来必力就没有QwQ

定时器：

leancloud有这么个休眠策略：

- 每天必须休眠 6 个小时
- 30 分钟内没有外部请求，则休眠。
- 休眠后如果有新的外部请求实例则马上启动（但激活时此次发送邮件会失败）。

在云引擎->定时任务->创建定时器里，一共有三栏：

名称随便填；函数选self-wake；下面选Cron表达式，里面填：

```
0 0/20 7-23 * * ?
```

别忘了启用定时器。（不知道要不要重启实例。。。）

这样就可以在每天7点到23点，每20分钟唤醒一次。在云引擎->应用日志可以看到它被唤醒的记录。

教程上说要设置什么Web主机域名，然而要实名认证很麻烦。。。结果不设置也能用？

>  参考：https://github.98.tn/hexo-valine-admin/

## gitalk/gitment

外观都还行，速度也不错。但是必须使用github登录才能评论。

好像gitment是原生的，最稳定。但是据说作者弃了一堆bug没人修。。。

---

# 加快博客访问速度

先放一个很有意思的网站：https://developers.google.com/speed/pagespeed/insights/ （可能需要翻墙？）

可以测试网站速度，给出评分、详细加载信息和优化建议。

## 改域名

blog里东西太多，加载起来很卡甚至有时候出不来东西，毕竟github是外国网站。网上说关联上coding能快很多，然而coding好像升级成了腾讯云开发者平台好像很麻烦的样子。。。

按教程搞了一遍后发现coding的访问速度和github差不了多少。。。~~所以还是我blog上东西太多了~~

慢慢找解决方案吧。。。（cry大佬说过买个域名会快？然而我太穷了）

2019/3/25：又试了一下发现了神奇的问题：晚上在家上github博客极慢甚至加载不出来，coding就很快；在机房正好反过来。。。（没有文明上网）

我也不知道该咋弄了。。。。

## 静态资源压缩

无意间看到的，什么html文件空行很多之类的原因会拖慢blog加载速度。

看到有很多gulp压缩的。但是配置起来比较麻烦，搞了半天还失败了。

又看到了hexo-neat插件，安装简洁，效果也不错。

安装：

``` bash
npm install hexo-neat --save
```

在站点配置文件中添加：

```
# 文件压缩，在exclude设置一些需要跳过的文件 
# hexo-neat
neat_enable: true
# 压缩 html
neat_html:
  enable: true
  exclude:
# 压缩 css
neat_css:
  enable: true
  exclude:
    - '**/*.min.css'
# 压缩 js
neat_js:
  enable: true
  mangle: true
  output:
  compress:
  exclude:
    - '**/*.min.js'
    - '**/jquery.fancybox.pack.js'
    - '**/index.js'
```

然后。。。就完了

执行`hexo g`部署时这个插件就会压缩资源（`hexo s`预览时也会压缩），会稍微慢一点。

在火狐下右键查看页面源代码就会发现很多东西都被压成了一行。。。

~~所以这证明了压行能提高程序效率~~

还有一个坑点：

刚用上就发现看板娘和鼠标点击特效不见了。。。

调了调发现它成功把我的看板娘$js,css$和鼠标特效的$js$压缩了，然后莫名出了BUG（不过一言的$js$压缩了没事？）

在站点配置文件中，`neat_css`的`exclude`里添加：

```
	- '**/waifu.css'
```

`neat_js`的`exclude`里添加：

```
    - '**/waifu-tips.js'
    - '**/autoload.js'
    - '**/love.js'
```

参考：https://www.playpi.org/2018112101.html

## 懒加载

懒加载就是不一次性完全加载图片，需要的时候再加载。

网上说可以安个插件：

```bash
npm install hexo-lazyload --save
```

在站点配置文件中添加：

```
lazyload:
  enable: true
  # className: #可选 e.g. .J-lazyload-img
  # loadingImg: #可选 eg. ./images/loading.png
```

然而。。。它报错了，没找到解决方法。

还有一种麻烦一点的方法：
~~懒得写了~~挂个链接吧：https://mmmmmm.me/hexo_er_lazy.html

然而。。。它又报错了。。。

$update$：发现为啥报错了，他的第一步里的$js$代码第一行`use strict'`少了个引号，应该是`'use strict'`

## cdn加速

### js加速

把国外的$cdn$源换成国内的~~大概~~会快一点

在主题配置文件中找到`vendors`一栏，把下面对应地方修改为：

```
vendors:
  # Internal path prefix. Please do not edit it.
  _internal: vendors
  # Internal version: 2.1.3
  jquery: https://cdn.bootcss.com/jquery/2.1.3/jquery.min.js
  # Internal version: 2.1.5
  # See: http://fancyapps.com/fancybox/
  fancybox: https://cdn.bootcss.com/fancybox/2.1.5/jquery.fancybox.pack.js
  fancybox_css: https://cdn.bootcss.com/fancybox/2.1.5/jquery.fancybox.min.css
  # Internal version: 1.0.6
  # See: https://github.com/ftlabs/fastclick
  fastclick: https://cdn.bootcss.com/fastclick/1.0.6/fastclick.min.js
  # Internal version: 1.9.7
  # See: https://github.com/tuupola/jquery_lazyload
  lazyload: https://cdn.bootcss.com/jquery_lazyload/1.9.7/jquery.lazyload.min.js
  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity: https://cdn.bootcss.com/velocity/1.2.1/velocity.min.js
  # Internal version: 1.2.1
  # See: http://VelocityJS.org
  velocity_ui: https://cdn.bootcss.com/velocity/1.2.1/velocity.ui.min.js
  # Internal version: 0.7.9
  # See: https://faisalman.github.io/ua-parser-js/
  ua_parser: https://cdn.bootcss.com/UAParser.js/0.7.9/ua-parser.min.js
  # Internal version: 4.6.2
  # See: http://fontawesome.io/
  fontawesome: https://cdn.bootcss.com/font-awesome/4.6.2/css/font-awesome.min.css
  # Internal version: 1
  # https://www.algolia.com
  algolia_instant_js:
  algolia_instant_css:
  # Internal version: 1.0.2
  # See: https://github.com/HubSpot/pace
  # Or use direct links below:
  # pace: //cdn.bootcss.com/pace/1.0.2/pace.min.js
  # pace_css: //cdn.bootcss.com/pace/1.0.2/themes/blue/pace-theme-flash.min.css
  pace:
  pace_css:
  # Internal version: 1.0.0
  # https://github.com/hustcc/canvas-nest.js
  canvas_nest: https://cdn.bootcss.com/canvas-nest.js/1.0.1/canvas-nest.min.js
  # three
  three: https://cdn.bootcss.com/three.js/r83/three.min.js
```

### jsdelivr

~~懒得写了~~这篇讲的就挺详细的：https://www.hojun.cn/2019/01/18/jsDeliver-github%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B%EF%BC%8C%E5%85%8D%E8%B4%B9%E5%A5%BD%E7%94%A8%E7%9A%84cdn/

这个东西我刚用的时候还是很快的，不过不知道为啥速度越来越慢。。。

### 全局cdn

正在研究。。。

> 参考：https://www.itcto.cn/blog/hexo%E5%8D%9A%E5%AE%A2%E4%BC%98%E5%8C%96/

## 图片压缩

[Tinypng](https://tinypng.com/)

[jpeg.io](https://www.jpeg.io/)

这俩网站都可以压缩图片，在几乎不损失清晰度情况下，平均能压缩$70\%$的大小。

然后`png`图片可以用格式工厂之类的软件改成`jpg(jpeg)`，大小也会减小。

$Tinypng$压缩效果更好，$jpeg.io$会自动转换成`jpg`格式。

---

# 自定义404页面

首先在根目录下的\souce里新建一个404.html文件，用html语言写。。。

$emm...$

我好像不会html...

方案：新建页面404$\rightarrow$在404页面里的index.md里用**markdown**语法写$\rightarrow$hexo g部署$\rightarrow$在根目录下的\public\404\index.html里的内容就是markdown转变成的html语言啦，粘过去就好了。

在站点配置文件中找到“skip_render”一项，改为：

```
skip_render: 404.html
```

如果有多项的话：

```
skip_render:
 - README.md
 - 404.html
```

（注意空格）

这样在访问了不存在的网页时就会跳到404页面上去了。

（404在本地预览时是看不到效果的，必须上传上去。。。）

附上我个人~~丑陋的~~404.html：

```html
<body>
  <div>
      <p><center><img src="https://i.loli.net/2019/04/09/5cac36216530c.jpg" width="340" height="350"></center></p>
    <p style="text-align: center;font-size:30px"><strong>啊嘞？网页丢了？</strong></p>
    <p style="text-align: center;font-size:20px"><a href="https://ctz45562.github.io"><strong>回到主页</strong></a></p>
    <p style="text-align: center;font-size:20px"><a href="javascript:history.go(-1);"><strong>返回上一页</strong></a></p>
    </div>
</html>
```

[效果](https://ctz45562.github.io/QwQ)

[~~抄袭~~参考来源](https://ouuan.github.io/hexo%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E6%8C%87%E5%8C%97/)（没错和一言那个是同一篇）

---
