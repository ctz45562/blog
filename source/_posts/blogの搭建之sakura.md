---
title: blogの搭建之sakura
date: 2019-06-04 12:42:45
categories: 颓
tags:
	- blog
description: 不知不觉写了两千多行了啊。。。
keywords: sakura
photos:  https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/blogddjzsakura.jpg
mathjax: true
---

索引：[blogの搭建之next](/2019/03/21/blogの搭建/)

不知不觉已经有两千多行了啊。。。~~虽然大部分是代码~~

甚至还有很多小地方我没写进去。如果当初搞一个更新日志的话，现在至少得有一百多条了吧。

<!--more-->

---

# 前言

很久以前在一个`wordpress`博客上看到了$sakura$主题，很对我口味。

然而我是`hexo`博客，不想转到`wordpress`上~~我现在也没钱买服务器~~，于是默默收藏了起来，要是以后我用`wordpress`就用这个主题。

前几天没事翻了翻`hexo`的主题，居然发现有人把$sakura$搬到了`hexo`上。

于是果断抛弃$next$立刻用了四天时间转到$sakura$主题并大量魔改，追求足够美观而尽量精简。

$sakura$很多配置当然比不上懒人必备的$next$，存在着很多能魔改的地方~~给了我颓废的契机~~，但教程实在是少。

只能凭借少量的教程、自己的$YY$和半吊子现学现卖的`html,css,js`来搞。~~（以及抄袭+搬代码）~~

本文记录我修改$sakura$主题的过程（也算是个教程），**其中部分内容同样适用于其他`hexo`主题**。同时声明一点：我会写在这里面的都是**网上没有教程或难以找到的东西，常见的问题还是请找万能百度或更万能的谷歌**。

在我退役前会不定期更新。

感谢以下教程对我的帮助：

> https://github.com/honjun/hexo-theme-sakura/blob/master/README-zh_cn.md
>
> https://yremp.club/2019/05/26/teach/
>
> https://blog.csdn.net/u014630987/article/details/78670258
>
> http://moxfive.xyz/2015/10/25/hexo-tag-cloud/
>
> https://github.com/wizardforcel/hexo-theme-landfarz/blob/master/layout/_partial/post/tag.ejs
>
> https://yremp.live/sakura-js/
>
> https://www.tomori.xyz/2019/06/15/emojis-plug-in-unit-for-bilibili/
>
> https://zxsama.top/7990d08f

也感谢`wordpress`作者[mashiro](https://2heng.xin/)和`hexo`作者[hojun](https://www.hojun.cn/)创造出$sakura$这一主题以及[Tian-Xing](https://tian-xing.github.io)对本文的纠错。

![](\images\blogの搭建之sakura-1.png)

---

# 基本配置

## Mathjax

### 纠错

`markdown`与$\LaTeX$冲突会导致公式渲染出错，尤其是两个下划线会被转移成斜体。

之前用$next$的时候是通过修改`kramed`源文件解决的，于是还是选择把`hexo-renderer-marked`换成`hexo-renderer-kramed`，请自行用`npm`安装。

在`\node_modules\kramed\lib\rules\inline.js` 中，第$11$行：

``` diff
-  escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
+  escape: /^\\([`*\[\]()#$+\-.!_>])/,
```

第$20$行：

``` diff
-  em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
+  em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

### 加速

一般$mathjax$源都是$cloudflare$，然而是国外的，速度怎么样我放张图自己体会：

![](\images\blogの搭建之sakura-4.png)

（[测速工具](https://ping.chinaz.com/)）

于是我把源换成了国内的$bootcdn$：

在`\themes\sakura\layout\_widget\common-article.ejs`中，修改：

``` diff
-  <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.4/latest.js?config=TeX-MML-AM_CHTML"></script>
+   <script type="text/javascript" src="https://cdn.bootcss.com/mathjax/2.7.6/latest.js?config=TeX-MML-AM_CHTML"></script>
```

在`\themes\sakura\source\js\sakura-app.js`中，修改：

``` diff
-         $.getScript('//cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.4/MathJax.js?config=TeX-MML-AM_CHTML', function () {
+         $.getScript('https://cdn.bootcss.com/mathjax/2.7.6/MathJax.js?config=TeX-MML-AM_CHTML', function () {
```

再 放 送：

![](\images\blogの搭建之sakura-5.png)

## 代码高亮

刚用的时候代码框十分诡异而且压根就没有高亮。找了几种方法并不可行。

最后发现应该是$hexo$自带的高亮冲突了，只要把站点配置文件中：

```
highlight:
  enable: true
```

$true$改为$false$就行了。

## 搜索

搜索一直不能用啊。。。

经过一晚上的配置毫无进展，最后在$hexo$博客群里问了下$dalao$，就是少了个插件。

`git bash`中执行：

```bash
npm install hexo-generator-json-content --save
```

## Fancybox

> update on 2020.1.12：
> 
> 去$sakura$的`github`页面发现其实已经配置好$Fancybox$了。。。只需要以`｛% fb_img src caption %｝`的格式引用即可。其中`src`为地址，`caption`为描述（可以不加）。
> 
> 不过这样是没法通过内嵌`html`自定义图片样式的，还是需要自己配置。

$Fancybox$就是那个点击图片进行幻灯片放映所有图片的东西。

$sakura$其实已经引入了$Fancybox$的`js`和`css`了，但是图片没有处理成对应的格式。

在`\themes\sakura\layout\_widget\common-article.ejs`中添加：

``` html
<script>
function imgInit(){
	var imgs=document.getElementsByClassName('entry-content').item(0).getElementsByTagName('img'),dsrc=record.getAttribute("data-src");
	for(var i=0;i<imgs.length;++i){
		var element = document.createElement("a"),record=imgs[i];
		element.setAttribute("data-fancybox","gallery");
		element.setAttribute("href",dsrc||imgs[i].src);
		imgs[i].parentNode.appendChild(element);
		imgs[i].parentNode.removeChild(imgs[i]);
		element.appendChild(record);
	}
}
imgInit();
</script>
```

## 添加`<!--more-->`支持

作为$next$前用户，我很喜欢`<!--more-->`的便捷性。

简单来说，`<!--more-->`截取了文章开头，以其原本的亚子显示。

然而$sakura$是不支持的，只能用`description`属性，不支持`markdown`和$\LaTeX$。

经过我的~~瞎改~~努力钻研，终于搞出来了。

### 主页

#### 添加

在`\themes\sakura\layout\_widget\index-items.ejs`中找到：

``` html
      <div class="float-content">
```

删除下面的：

``` html
        <p style="text-align:left"><%= post.description %></p>
        <div class="post-bottom">
          <a href="<%- url_for(post.path) %>" class="button-normal">
            <i class="iconfont icon-caidan"></i>
          </a>
        </div>
```

在上面添加：

``` html
	  	<% if(post.excerpt) { %>
		<div class="readmore">
		  <%- post.excerpt %>
		 </div>
		<% } else { %>
		<p style="text-align:left"><%= post.description %></p>
		<% } %>
```

#### 配置样式

在`\themes\sakura\sourcr\css\style.css`中，添加：

``` css
.readmore{
	text-align:left;
	position:relative;
	margin-top:10px;
}
.readmore a {
    color:#08f;
    text-decoration:underline dotted rgba(0,0,0,.1)
}
.readmore a:hover {
    color:#05f !important;
    text-decoration:underline #05f
}
.readmore p {
    color:#5c5c5c;
	line-height:27px;
	margin: 0;
}
.readmore hr {
    margin-top:40px;
    margin-bottom:40px;
    display:block;
    border:0;
    text-align:center;
    background:none
}
.readmore hr:before {
    display:inline-block;
    margin-left:.6em;
    color:rgba(0,0,0,.8);
    position:relative;
    top:-30px;
    font-size:28px;
    letter-spacing:.6em
}
.readmore img{transform:none!important;}
.readmore blockquote {
	margin-bottom: 10px;
    padding:10px 15px;
	border-radius: 1.3rem;
}
.readmore blockquote:before {
    font-size:1.2rem;
    top:-5px;
    left:-2px;
}
.readmore blockquote:after {
    font-size:1.2rem;
    bottom:-15px;
    right:0px;
}
```

这里我只添加了`<a>,<p>,<hr>,<blockquote>`的样式和对`<img>`$BUG$的修复，其他有需要的得把`.entry-content`的搬过来。

这样有`<!--more-->`标签的会优先显示，否则显示`description`。

#### js支持

一些仅用于文章里的`js`例如$\LaTeX$也要作用于`<!--more-->`。

以$\LaTeX$为例：

添加到`\themes\sakura\layout\index.ejs`中。

``` html
  <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.4/latest.js?config=TeX-MML-AM_CHTML"></script>
  <script type="text/x-mathjax-config">
    MathJax.Hub.Config({tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}});
  </script>
```

其他的例如$Fancybox$和下文的模糊字体请自行添加。

#### 解决「显示更多」js不加载问题

这里用了个非常丑陋的方法：暴力设定时器。也懒得改了。

在`\themes\sakura\source\sakura-app.js`中找到函数：

``` javascript
mashiro_global.post_list_show_animation = new function () {
```

整个修改为：

``` javascript
var previousFlag=false;
mashiro_global.post_list_show_animation = new function () {
  this.ini = function (ajax) {
    $('article.post-list-thumb').each(function (i) {
      if (ajax) {
        var window_height = $(window).height()
      } else {
        if ($('.headertop').hasClass('headertop-bar')) {
          var window_height = 0
        } else {
          var window_height = $(window).height() - 300
        }
      }
      if (!mashiro_global.landing_at_home) {
        window_height += 300
      }
      var article_height = $('article.post-list-thumb').eq(i).offset().top
      if ($(window).height() + $(window).scrollTop() >= article_height) {
        $('article.post-list-thumb').eq(i).addClass('post-list-show')
		previousFlag=true;
      }
      $(window).scroll(function () {
        var scrolltop = $(window).scrollTop()
        if (scrolltop + window_height >= article_height && scrolltop) {
          $('article.post-list-thumb').eq(i).addClass('post-list-show')		
		  previousFlag=true;
        }
      })
    })
  }
}()
setInterval(function(){
if(previousFlag && MathJax){
	previousFlag=false;
	if(MathJax)MathJax.Hub.Queue(["Typeset", MathJax.Hub]);
}
},1000);
```

其他的请自行添加到定时器`setInterval`里。

### 文章列表

文章列表用`<!--more-->`截断会比较丑陋，空白区域太小了还不支持换行。

一开始我保留了`desciription`，但因为两者差距太大了，主页和文章列表无法统一。我强迫症犯了，直接暴力解决：删掉文章描述！

光删掉之后太空荡了，于是用文章信息如日期、分类、标签代替了，右上角的日期就去掉了。

在`\themes\sakura\layout\_widget\category-items.ejs`中，找到：

``` html    
  <div class="p-time">
      <i class="iconfont icon-time">
      </i>
    <%= date(post.date, 'YYYY-M-D') %></div>
```

替换为：

``` html
<div class="category-meta" style="margin:8px 0 0 17.4%">
        <span><i class="fa fa-calendar"></i>&nbsp;
          <%= date(post.date, 'YYYY-M-D') %></span><em>·</em>
        <span>
        <i class="fa fa-archive"></i>&nbsp;
        <% post.categories.each((category)=>{ %>
          <a href="<%- url_for(category.path) %>" class="set-color"><%= category.name %><em>·</em></a>
        <% }) %>
        </span>
        <span><i class="fa fa-tags"></i>
        <% post.tags.each((tag)=>{ %>
            <a href="<%- url_for(tag.path) %>" class="set-color"><%= tag.name %><em>·</em></a>
          <% }) %>
        </span>
        <style>
        .set-color:hover{color: #3ca0ff;}
        </style>
      </div>
```
其中`<style>`里面规定了链接颜色，我用的自己的主题色，需要自行修改（默认为`orange`）。

在`\themes\sakura\source\css\style.css`中，添加：
``` css
.category-meta,.category-meta a {
    color:#888;
    font-size:13px
}
.category-meta em {
    margin: 0 5px;
    color: #666;
}
```

---

# 大清洗

为了追求简洁，去掉了不少东西。

## 网易云播放器

目前我并不想把音乐放到$blog$上，留着影响加载速度。

在`\themes\sakura\layout\layout.ejs`里注释掉下面这两行：

```html
  <%- partial('_partial/mheader', null, {cache: !config.relative_link}) %>
  <%- partial('_partial/aplayer', null, {cache: !config.relative_link}) %>
```

这样就可以了。如果想再精简的话可以在`\themes\sakura\source\css\style.css`里注释掉有关`aplayer`的部分。

## 主页的视频功能

主页右下角有一个播放键，可以放上视频播放的。

显然我用不着。。。

在`\themes\sakura\layout\_partial\headertop.ejs`中**只**注释掉下面这两行：

```html
    <div id="video-btn" class="loadvideo videolive">
    </div>
```

不知道为啥注释掉其他有关`video`的部分会出现一些神奇的$bug$

## 打赏和转载

在`\themes\sakura\layout\_widget\common-articles.ejs`中注释掉以下部分：

```html
<div class="single-reward">
  <div class="reward-open">赏<div class="reward-main">
    <ul class="reward-row">
      <li class="alipay-code"><img src="<%- (theme.cdn || '') + theme.donate.alipay%>"></li>
      <li class="wechat-code"><img src="<%- (theme.cdn || '') + theme.donate.wechat%>"></li>
    </ul>
  </div>
</div>
</div>
<div style="text-align:center; width: 100%" class="social-share share-mobile" data-disabled="diandian, tencent"></div>
<footer class="post-footer">
  <div class="post-lincenses"><a href="https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh" target="_blank" rel="nofollow"><i class="fa fa-creative-commons" aria-hidden="true"></i> 知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a></div>
  <div class="post-tags">
  </div>
  <div class="post-share">
  <div class="social-share sharehidden share-component"></div>
  <i class="iconfont show-share icon-forward"></i>
</div>
</footer><!-- .entry-footer -->
```

## 复制附加版权信息

复制的时候会额外加上一段“著作权归作者所有。。。”，很不方便。

在`\themes\sakura\source\js\sakura-app.js`中找到：

```javascript
    var htmlData = '' + '著作权归作者所有。<br>' + '商业转载请联系作者获得授权，非商业转载请注明出处。<br>' + '作者：' + mashiro_option.author_name + '<br>' + '链接：' + window.location.href + '<br>' + '来源：' + mashiro_option.site_name + '<br><br>' + window.getSelection().toString().replace(/\r\n/g, '<br>')
    var textData = '' + '著作权归作者所有。\n' + '商业转载请联系作者获得授权，非商业转载请注明出处。\n' + '' + mashiro_option.author_name + '\n' + '链接：' + window.location.href + '\n' + '来源：' + mashiro_option.site_name + '\n\n' + window.getSelection().toString().replace(/\r\n/g, '\n')
```

改为：

``` javascript
    var htmlData = window.getSelection()
    var textData = window.getSelection()
```

如果要去掉复制成功的信息的话删去：

``` javascript
      addComment.createButterbar('复制成功！<br>Copied to clipboard successfully!', 1000)
```

## 页面底部版权信息和转发功能

在`\themes\sakura\layout\_partial\footer.ejs`中，注释掉：

```html
  <p style="color: #666666;">&copy 2018</p> 
```

在`\themes\sakura\layout\_widget\common-page.ejs`中，删除：

```html
      <footer class="post-footer">
        <div class="post-lincenses"><a href="https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh" target="_blank" rel="nofollow"><i class="fa fa-creative-commons" aria-hidden="true"></i> 知识共享署名-非商业性使用-相同方式共享 4.0 国际许可协议</a></div>
        <div class="post-tags">
        </div>
        <div class="post-share">
          <div class="social-share sharehidden share-component"></div>
          <i class="iconfont show-share icon-forward"></i>
        </div>
      </footer><!-- .entry-footer -->
    </article>
      <!-- #post-## -->
      <section class="author-profile">
        <div class="info" itemprop="author" itemscope="" itemtype="http://schema.org/Person">
          <a href="/about/" class="profile gravatar"><img src="<%- (theme.cdn || '') + theme.avatar%>" itemprop="image" alt="<%- theme.siteName %>" height="70" width="70"></a>
          <div class="meta">
            <span class="title">Author</span>
            <h3 itemprop="name">
            <a href="<%- theme.url%>" itemprop="url" rel="author"><%- theme.siteName %></a>
            </h3>
          </div>
        </div>
        <hr>
        <p><i class="iconfont icon-write"></i><%- theme.description%></p>
      </section>
```

## 代码框打开功能

点击代码框的标题会全屏显示代码，感觉很影响阅读体验。

在`\themes\sakura\source\js\sakura-app.js`中，注释掉：

```javascript
  $('pre').on('click', function (e) {
    if (e.target !== this) return
    $(this).toggleClass('code-block-fullscreen')
    $('html').toggleClass('code-block-fullscreen-html-scroll')
  })
```

---

# 外观美化

## 个性化css

$next$有一个`custom.styl`文件可以自己写$css$，于是给$sakura$搞了一个。

其实很简单，在`\themes\sakura\source`下新建一个`code.css`的文件，在`\themes\sakura\layout\layout.ejs`中第二行`<body class="...">`下面添加：

```html
  <style type="text/css">
    @import url("/code.css");
  </style>
```

就可以在`code.css`里编辑$css$啦。比如改鼠标样式、字体都可以在里面搞。

## 主题工具

就是左下角那个。

这玩意我研究了很长时间。也非常实用，在保留简洁性的前提下给访客高度的个性化能力。

### 添加

在`themes\sakura\layout\layout.ejs`中：

``` html
  <div class="scrollbar" id="bar">
  </div>
```

前面添加：


```html
 <%- partial('_partial/setdisplay') %> 
 <%- partial('_partial/set', null, {cache: !config.relative_link}) %>
```

原版是在` <%- partial('_partial/mheader', null, {cache: !config.relative_link}) %>`前添加的，但要这样字体切换会出$bug$。

在`\themes\sakura\layout\_partial`中新建`set.ejs`，内容：

```html
<div class="changeSkin-gear no-select"> 
    <div class="keys" id="setbtn"> 
        <span id="open-skinMenu"> 
            SCHEME TOOL | 主题工具 &nbsp;
            <i class="iconfont icon-gear inline-block rotating">
            </i> 
        </span>
    </div> 
</div>
```

新建`setdisplay.ejs`，内容：

```html
<div class="skin-menu no-select" id="mainskin" style="position: fixed"> 
    <div class="theme-controls row-container">
          <p style="text-align:center;font-family:'Monaco';font-weight:bold;color:#444"><i style="color:grey" class="fa fa-chevron-left"></i> background <i style="color:grey" class="fa fa-chevron-right"></i></p>
        <ul class="menu-list"> <li id="white-bg"> 
            <i class="fa fa-television" aria-hidden="true">
            </i>
            </li> 
            <li id="sakura-bg"> 
                <i class="iconfont icon-sakura">
                </i>
            </li>
            <li id="gribs-bg">
                <i class="fa fa-slack" aria-hidden="true">
                </i>
            </li>
            <li id="KAdots-bg">
                <i class="iconfont icon-dots">
                </i>
            </li>
            <li id="totem-bg">
                <i class="fa fa-optin-monster" aria-hidden="true">
                </i>
            </li>
            <li id="pixiv-bg">
                <i class="iconfont icon-pixiv">
                </i>
            </li>
            <li id="bing-bg">
                <i class="iconfont icon-bing">
                </i>
            </li>
            <li id="dark-bg">
                <i class="fa fa-moon-o" aria-hidden="true">
                </i>
            </li>
        </ul>
    </div>
    <canvas id="night-mode-cover">
    </canvas>
</div>
```

还要修点$bug$：

在`\themes\sakura\source\js\sakura-app.js`中，找到函数`$('.skin-menu #dark-bg').click(function ()`，函数最底下添加：

``` javascript
setCookie('bgImgSetting','https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/other/starry_sky.png',30)
```

把所有形如：

``` javascript
$('.changeSkin-gear, .toc').css('background', 'none')
或
$('.changeSkin-gear, .toc').css('background', 'rgba(255,255,255,0.8)')
```

里的`.changeSkin-gear,`删掉。

这只是初始版，后面还有更高级的。

### 更换图片

$bing$主题是从$bing$随机图片`api`获取一张图片做背景，可以更换。

在`\themes\sakura\source\js\sakura-app.js`中，下面两句：

``` javascript
 changeBGnoTrans('#bing-bg', 'https://api.shino.cc/bing/')
```

``` javascript
else if (bgurl == 'https://api.shino.cc/bing/')
```

其中的网址换成其他`url`。

[随机图片url可以点这里哦](/2019/02/01/随机图片url整合/)

其他主题类似，找到`url`位置修改。

### 位置和外观

在`set.ejs`里直接用`css`美化，可能还要修正`setdisplay.ejs`。

参考配置：

`set.ejs`：

``` html
<div class="changeSkin-gear no-select" style="background: rgba(0, 0, 0, 0) none repeat scroll 0% 0%; visibility: visible; bottom: 0px;"> 
  <div class="keys" id="setbtn"> 
    <button id="open-skinMenu">
		<style>
		button#open-skinMenu{
			transition: all 0.2s linear 0s;
            outline:none;
			position:fixed;
			bottom:13px;
			left:15px;
			font-size:16px;
			background-color: rgba(255,255,255,.95);
			border-radius: 20px;
			box-shadow: 0 3px 8px 0 rgba(0,0,0,0.1), 0 3px 8px 0 rgba(0,0,0,0.1);
		}
		button#open-skinMenu:hover{
		    transition: all 0.2s linear 0s;
			background-color: rgb(255, 165, 0);
			color: rgba(255,255,255);
		}
		</style>
		<i class="iconfont icon-gear inline-block rotating">
	  </i> 
		SCHEME TOOL | 主题工具 
	</button>
  </div> 
</div>
```

其中：

``` css
			position:fixed;
			bottom:13px;
			left:15px;
```

用于把位置移到左边去，不需要可以删掉；如果需要，还要更改`setdisplay.ejs`（第一行）：

``` html
<div class="skin-menu no-select" id="mainskin" style="position: fixed;bottom:65px;left:31px;"> 
```

### 字体切换

`wordpress`上有字体切换功能。

翻了翻`sakura-app.js`，发现字体切换功能函数已经写好了，直接用就行。

`setdisplay.ejs`中，在`<canvas id="night-mode-cover">`前添加：

``` html
  <hr>
  <p style="text-align:center;font-family:'Monaco';font-weight:bold;color:#444"><i style="color:grey" class="fa fa-chevron-left"></i> font <i style="color:grey" class="fa fa-chevron-right"></i></p>
  <div class="font-family-controls row-container">
    <button type="button" class="control-btn-serif " data-mode="serif" onclick="mashiro_global.font_control.change_font()">Serif</button>
    <button type="button" class="control-btn-sans-serif" data-mode="sans-serif" onclick="mashiro_global.font_control.change_font()">Sans Serif</button>
  </div>
```

在`sakura-app.js`中，找到：

``` javascript
  this.ini = function () {
    if (document.body.clientWidth > 860) {
      if (!getCookie('font_family') || getCookie('font_family') == 'serif') { $('body').addClass('serif') }
    }
    if (getCookie('font_family') == 'sans-serif') {
      $('body').removeClass('sans-serif')
      $('.control-btn-serif').removeClass('selected')
      $('.control-btn-sans-serif').addClass('selected')
    }
  }
```

改为：

``` javascript
  this.ini = function () {
	var font = getCookie('font_family')
    if (document.body.clientWidth > 860) {
      if (! font || font == 'serif') { 
	    $('body').addClass('serif') 
		$('.control-btn-serif').addClass('selected')
	  }
    }
    if (font == 'sans-serif') {
      $('body').addClass('sans-serif').removeClass('serif')
      $('.control-btn-sans-serif').addClass('selected')
    }
  }
```

在`\themes\sakura\source\css\style.css`中，找到`.font-family-controls button {`，下面添加：

``` css
	outline: none;
```

找到：

``` css
.serif {
    font-family:'Source Han Serif SC','Source Han Serif','source-han-serif-sc','PT Serif','SongTi SC','MicroSoft Yahei',Georgia,serif
}
```

改为：

``` css
.serif {
    font-family:'Noto Serif SC', 'Source Han Serif SC', 'Source Han Serif', source-han-serif-sc, 'PT Serif', 'SongTi SC', 'MicroSoft Yahei', Georgia, serif
}
```

换字体后主页的`startdash`可能会被挤变形，需要在`style.css`中找到：

``` css
.top-feature-row {
    width:100%;
    height:auto;
    margin-top:55px
}
```

把`width`改大点就行。

### 加特技

本人的创新，添加背景特效及开关。

樱花雨特效来自[yremp.live](https://yremp.live)。彩带特效来自主题[butterfly](https://github.com/jerryc127/hexo-theme-butterfly)。

（顺便吐槽一下彩带特效：彩带只会在较靠上的区域生成。当文章较长时，翻到下面彩带就出不来了。~~咱也看不懂js源码也不会改~~）

`setdisplay.ejs`中，在`<canvas id="night-mode-cover">`前添加：

``` html
  <hr>
  <p style="text-align:center;font-family:'Monaco';font-weight:bold;color:#444"><i style="color:grey" class="fa fa-chevron-left"></i> script <i style="color:grey" class="fa fa-chevron-right"></i></p>
  <div class="theme-controls row-container">
  <ul class="menu-list">
	<li id="empty-effect">
	  <i class="fa fa-ban">
	  </i>
	</li>
	<li id="sakura-rain-effect">
	  <i class="iconfont icon-sakura">
	  </i>
	</li>
	<li id="snowy-effect">
	  <i class="fa fa-snowflake-o">
	  </i>
	</li>
	<li id="lines-effect">
	  <i class="fa fa-chevron-left">
	  </i>
	</li>
    <li id="colorful-belts-effect">
	  <i class="fa fa-map"></i>
	  </i>
	</li>
	<li id="words-rain-effect">
	  <i class="fa fa-font"></i>
	</li>
	<li id="point-rain-effect">
	  <i class="iconfont icon-dots"></i>
	</li>
	<li id="rain-drop-effect">
	  <i class="fa fa-tint"></i>
	</li>
  </ul>
  </div>
```

在`\themes\sakura\source\js\sakura-app.js`中，找到：

``` javascript
  function closeSkinMenu () {
```

前面添加：

``` javascript
  $('.skin-menu #empty-effect').click(function(){
	sakuraEffectClear()
	snowEffectClear()
	lineEffectClear()	
    beltEffectClear()
	wordEffectClear()	
    pointEffectClear()
	rainEffectClear()
	closeSkinMenu()
  })

  $('.skin-menu #sakura-rain-effect').click(function(){
	var effect = sakuraEffectClear()
	if(!effect) {
		effect = document.createElement("script")
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/yremp/yremp-js@1.5/sakura.js")
		effect.setAttribute("id","sakura-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('sakuraEffectCookie','use',30)
	}
	closeSkinMenu()
  })
  $('.skin-menu #snowy-effect').click(function(){
	var effect = snowEffectClear()
	if(!effect){
		effect = document.createElement("script")		
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.3.3/js/snow.js")
		effect.setAttribute("id","snow-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('snowyEffectCookie','use',30)
	}
	closeSkinMenu()
  })
  $('.skin-menu #lines-effect').click(function(){
	var effect = lineEffectClear()
	if(!effect){
		effect = document.createElement("script")		
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.3.3/js/line.js")
		effect.setAttribute("id","line-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('linesEffectCookie','use',30)
	}	
	closeSkinMenu()
  })
  $('.skin-menu #colorful-belts-effect').click(function(){
    var effect = beltEffectClear()
	if(!effect){
		effect = document.createElement("script")		
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.0/js/piao.js")
		effect.setAttribute("id","belt-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('beltsEffectCookie','use',30)
	}
	closeSkinMenu()
  })
  $('.skin-menu #words-rain-effect').click(function(){
	var effect = wordEffectClear()
	if(!effect){
		effect = document.createElement("script")		
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.0/js/coderain.js")
		effect.setAttribute("id","words-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('wordsEffectCookie','use',30)
	}
	closeSkinMenu()
  })
  $('.skin-menu #point-rain-effect').click(function(){
	  var effect = pointEffectClear()
      if(!effect){		
	    effect = document.createElement("script")		
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.3/js/pointrain.js")
		effect.setAttribute("id","point-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('pointEffectCookie','use',30)
	  }
	  closeSkinMenu()
  })
  $('.skin-menu #rain-drop-effect').click(function(){
	  var effect = rainEffectClear()
      if(!effect){		
	    effect = document.createElement("script")
		effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.5/js/raindrop.js")
		effect.setAttribute("id","raindrop-effect")
		document.getElementsByTagName("body").item(0).appendChild(effect)
		setCookie('rainEffectCookie','use',30)
	  }
	  closeSkinMenu()
  })
  function sakuraEffectClear(){
	var effect = document.getElementById("sakura-effect")
	if(effect){
		effect.parentNode.removeChild(effect)
		effect = document.getElementById("canvas_sakura")
		effect.parentNode.removeChild(effect)
		setCookie('sakuraEffectCookie','',30)
	}
	return effect;
  }
  function snowEffectClear(){
	  var effect = document.getElementById("snow-effect")
	  if(effect){
		effect.parentNode.removeChild(effect)
		clearInterval(CIYANG)
		var snow = document.getElementById("snowbox")
		while(snow){
			snow.parentNode.removeChild(snow)
			snow = document.getElementById("snowbox")
		}
		setCookie('snowyEffectCookie','',30)
	  }
	  return effect
  }
  function lineEffectClear(){
	  var effect = document.getElementById("line-effect")
	  if(effect){
		  effect.parentNode.removeChild(effect)
		  var lines = document.getElementById("lines")
		  lines.parentNode.removeChild(lines)
		  setCookie('linesEffectCookie','',30)
	  }
	  return effect
  }
  function beltEffectClear(){
	  var effect = document.getElementById("belt-effect")
	  if(effect){
		  effect.parentNode.removeChild(effect)
		  effect = document.getElementById("belts1")
		  effect.parentNode.removeChild(effect)
		  setCookie('beltsEffectCookie','',30)
	  }
	  return effect
  }
  function wordEffectClear(){
	  var effect = document.getElementById("words-effect")
	  if(effect){
		  effect.parentNode.removeChild(effect)
		  effect = document.getElementById("coderain")
		  effect.parentNode.removeChild(effect)
		  setCookie('wordsEffectCookie','',30)
	  }
	  return effect
  }
  function pointEffectClear(){
	  var effect = document.getElementById("point-effect")
	  if(effect){
		  effect.parentNode.removeChild(effect)
		  effect = document.getElementById("point")
		  effect.parentNode.removeChild(effect)
		  setCookie('pointEffectCookie','',30)
	  }
	  return effect
  }
  function rainEffectClear(){
	  var effect = document.getElementById("raindrop-effect")
	  if(effect){
		  effect.parentNode.removeChild(effect)
		  document.body.removeChild(document.getElementById('rain'))
		  setCookie('rainEffectCookie','',30)
	  }
	  return effect
  }
```

找到：

``` javascript
if (document.body.clientWidth > 860) {
  checkBgImgCookie()
}
```

改为：

``` javascript
function checkEffectsCookie() {
  var efurl = getCookie('sakuraEffectCookie')
  if(efurl) {
	var effect = document.createElement("script")
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/yremp/yremp-js@1.5/sakura.js")
	effect.setAttribute("id","sakura-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }
  efurl = getCookie('snowyEffectCookie')
  if(efurl) {
	var effect = document.createElement("script")		
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.3.3/js/snow.js")
	effect.setAttribute("id","snow-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }
  efurl = getCookie('linesEffectCookie')
  if(efurl) {
	var effect = document.createElement("script")		
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.3.3/js/line.js")
	effect.setAttribute("id","line-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }
  efurl = getCookie('beltsEffectCookie')
  if(efurl){
	var effect = document.createElement("script")		
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.0/js/piao.js")
	effect.setAttribute("id","belt-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }
  efurl = getCookie('wordsEffectCookie')
  if(efurl){
	var effect = document.createElement("script")		
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.0/js/coderain.js")
	effect.setAttribute("id","words-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }  
  efurl = getCookie('pointEffectCookie')
  if(efurl){
	var effect = document.createElement("script")		
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.3/js/pointrain.js")
	effect.setAttribute("id","point-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }
  efurl = getCookie('rainEffectCookie')
  if(efurl){
	var effect = document.createElement("script")
	effect.setAttribute("src","https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.5/js/raindrop.js")
	effect.setAttribute("id","raindrop-effect")
	document.getElementsByTagName("body").item(0).appendChild(effect)
  }
}
if (document.body.clientWidth > 860) {
  checkBgImgCookie()
  checkEffectsCookie()
}
```

注：我使用了`iconfont`图标，若按上文教程食用与我博客上的会不一样，需要的请自行更改。

---

### 拓展

在原始主题中，文章顶端的图片很扁（比例大概是$4:1$？），个人觉得不美观，就改成其他主题的排版（比例$2:1$）：

说实话自己也没弄明白怎么搞的，可能会出$BUG$，怕误人子弟就不写了。

有兴趣的可以找我私聊。

## 更换时间和阅读数的图标

就是文章顶部的日历和眼睛图标。

在`\themes\sakura\layout\_widget\common-article.ejs`中：

```html
        ·</span>
        <%= date(post.date, 'YYYY-M-D') %><span class="bull">
```

`</span>`前的`·`换成`<i class="fa fa-calendar" aria-hidden="true"></i>`

```html
        ·</span>
      <span id="busuanzi_value_page_pv"></span>次阅读</p>
```

`</span>`前的`·`换成`<i class="fa fa-eye" aria-hidden="true"></i>`

## 首页文章描述左对齐

$update$：现在已经加上了`<!--more-->`支持，其中所有的文字为显示正常均调成左对齐。

吐槽：`sakura`是不支持`<!--more-->`阅读全文的，只能用文章的`description`元素设置。这样就不支持`markdown`和$\LaTeX$了。。。

首页中，图片靠左的文章描述（`description`）是右对齐的，看着很别扭。

在`\themes\sakura\layout\_widget\index-items.ejs`中，找到：

```html
<p><%= post.description %></p>
```

修改为：

```html
<p style="text-align:left"><%= post.description %></p>
```

## 分割线

为啥原版的`<hr>`都是三个点啊。。。

感谢[Tian-Xing](https://tian-xing.github.io)找到了`<hr>`样式的地方。

本以为是通过很深层的`html`改的，没想到是`css`。。。

在`\themes\sakura\source\css\style.css`删除：

``` css
hr { 
    box-sizing:content-box;
    height:0
} 
```

``` css
hr {
    background-color:#ccc;
    border:0;
    height:1px;
    margin-bottom:1.5em
}
```

`.entry-content hr:before `里的：

``` css
    content:'...';
```

最底下添加：

``` css
hr {
	height: 3px !important;
	border: none !important;
	background-color: #bfbfbf !important;
	background-image: repeating-linear-gradient(-45deg, #fff, #fff 4px, transparent 4px, transparent 8px) !important;
}
```

~~（搬的next的样式）~~

这还没完，因为除归档外所有列出文章的地方都用`<hr>`分割了，`css`同样会起作用。

那就把`<hr>`改成`<br>`呗：

在`\themes\sakura\layout\_widget\category-items.ejs`中，将倒数第二行的`<hr>`改为`<br>`。

还有`NEXT POST`和`PREVIOUS POST`按钮也要改。直接删除`\themes\sakura\layout\_widget\common-article.ejs`中，`<%= post.prev.title%></h3>`和`<%= post.next.title%></h3>`下面的`<hr>`。

## 代码行高亮

`wordpress`上的$sakura$里，鼠标放到代码上，对应行会高亮。

$F12$查看元素属性就会发现只是一个简单的`css`实现。

在`\themes\sakura\source\css\style.css`（如果您配置了个性化`css`的话也可以在`code.css`里）中添加：

``` css
.hljs-ln-line:hover {
    background-color: rgba(255, 255, 255, .1);
}
.hljs-ln-line.hljs-ln-n:hover{
	background-color: rgba(0,0,0,0) !important;
}
```

如果没有下面那条，鼠标放在行号上行号会高亮。。。

## 表情

来自https://www.tomori.xyz/2019/06/15/emojis-plug-in-unit-for-bilibili/ 

博客源文件夹下安装：

``` 
npm i hexo-tag-emojis-bycoda --save
```

这个版本有点低，到[github](https://github.com/TomoriCoda/hexo-tag-emojis-bycoda)下载压缩包，将里面的文件覆盖掉`\node_modules\hexo-tag-emojis-bycoda`的内容。

在`\themes\sakura\layout\_partial\head.ejs`中，`<link rel="stylesheet" href="/css/zoom.css" media="all">`下添加：

``` html
  <style>
  .emoji-coda {
      display: inline-block !important;
      position: relative;
      width: 45px;
      top: 2px;
      margin: -3px 3px !important;
      padding: 0;
  }
  </style>
```

修改`width`一栏可以调整表情大小。

使用方式：

在`\node_modules\hexo-tag-emojis-bycoda\emojis`找到想用的表情，格式：

```markdown
{% emoji_coda 文件夹/表情名%}
```

示例：

```markdown
表情示例（喝水）{% emoji_coda 2233/heshui %}
```

表情示例（喝水）{% emoji_coda 2233/heshui %}

虽然可以直接引用图片实现，但这样表情可以和文字位于一行，而且大小已经预设好了，使用方便。

## 更改主题颜色

心血来潮把颜色翻新了一遍。

### 整体

$sakura$颜色以橙色为主，将所有文件（应该只有`style.css`、`sakura-app.js`、`head.ejs`）里`orange`和`#FE9600`替换为想要的颜色。

最好是了解十六进制颜色码或`rgb/rgba`来选择颜色。

主题工具和归档页面的按钮颜色要手动改。

### 链接

在`\themes\sakura\source\css\style.css`里找到`.entry-content a`修改`color`。

若要修改鼠标放上去的颜色，修改下面的`.entry-content a:hover`的`color`即可。

### 代码块

在`style.css`里找到`.entry-content code`修改`color`和`background`。

### 搜索框

修改鼠标放到搜索结果上时背景的颜色。

在`\themes\sakura\source\css\insight.styl`中，修改第六行的`ins-background-orange`。

## 文章信息文字阴影加深

我写文章都会配二次元图，有很多浅色图片，导致文章的标题、时间、阅读量等信息与背景融为一体：

![](\images\blogの搭建之sakura-2.png)

于是加深了一下阴影。在`\themes\sakura\layout\_widget\common-article.ejs`中，修改：

``` diff
- <header class="pattern-header single-header">
+ <header class="pattern-header single-header" style="text-shadow: 0 0 7px #000,0 0 7px #000">
```

效果还是很明显的：

![](\images\blogの搭建之sakura-3.png)

## 修改文章配图height

在`\themes\sakura\source\css\style.css`中，找到：

``` css
.pattern-attachment-img {
    background-repeat:no-repeat;
    background-size:cover;
    background-position:center center;
    background-origin:border-box;
    width :100%;
    height:400px
}
```

你会找到两个。。。估计是作者的$BUG$，删掉其中一个，修改另一个的`height`即可。

## 文章列表图片拉伸问题の解决

其实一直想修。。。

浏览某个分类或标签下的文章下时，配图是被**压缩**成正方形的：

![](\images\blogの搭建之sakura-6.png)

度娘发现了一个神奇的`css`属性：`object-fit`。

在`\themes\sakura\source\css\style.css`中，找到`.feature img`，内部添加：

``` css
	object-fit: cover;
```

![](\images\blogの搭建之sakura-7.png)

## 引用样式修改

对引用内容的样式进行了一定的修改。

修改内容包括：

- 引号大小缩小
- 增添浅灰色背景色
- 边框与引号的位置自适应文本

在`\themes\sakura\source\css\style.css`中，把`blockquote`、`blockquote:before`、`blockquote:after`三项的内容替换为：

``` css
blockquote {
    width:fit-content;
	width:-webkit-fit-content;
	width:-moz-fit-content;
	width:-ms-fit-content;
	width:-o-fit-content;
    margin:0;
	margin-bottom: 20px;
    padding:12px 45px;
    position:relative;
	border-radius: 1.5rem;
	background: rgba(225, 225, 225, .43);
}
blockquote:before {
    content:"\f10d" !important;
    font-size:2rem;
    position:absolute;
    top:-25px;
    left:2px;
    color:#50bdff;
    font-family:FontAwesome
}
blockquote:after {
    content:'\f10e' !important;
    font-size:2rem;
    position:absolute;
    bottom:-25px;
    right:0px;
    color:#50bdff;
    font-family:FontAwesome
}
```

主要还是为了我自己看着顺眼。{% emoji_coda xiaodianshi/xiao %}

## 主页startdash美（丑）化

为了实用性舍弃美观性。

$sakura$主页的`startdash`一栏我都是用来当置顶文章的。但是通过观看了他人访问我的blog的过程和我亲自体验，发现这玩意太不明显了。

上面写着一个颜色很浅难以察觉的`top`，下面几张图片，怎么看都像是图片展览，除非把鼠标放上去才会发现是文章链接。

于是我直接把标题改到外面去了，同时对其样式进行了调整：

在`\themes\sakura\source\css\style.css`中：

将`.the-feature.from_left_and_right .info`里的内容替换为：

``` css
    position:absolute;
    top:0;
    bottom:0;
    left:0;
    right:0;
    text-align:center;
    -webkit-backface-visibility:hidden;
    backface-visibility:hidden;
    background:transparent;
    -webkit-transition:all .35s ease-in-out;
    -moz-transition:all .35s ease-in-out;
    transition:all .35s ease-in-out
```

将`.the-feature.from_left_and_right .info h3`里的内容替换为：

``` css
    text-transform:uppercase;
    color:rgba(255, 255, 255);
    text-align:center;
    font-size:22px;
    letter-spacing:1px;
    text-shadow: 0 0 8px #000,0 0 8px #000;
    font-weight:bold;
    padding:10px;
    margin:30px 0 0 0;
    background:transparent;
    -webkit-transition:all .35s ease-in-out;
    -moz-transition:all .35s ease-in-out;
    transition:all .35s ease-in-out;
    -webkit-transform:translateY(50%);
    -moz-transform:translateY(50%);
    -ms-transform:translateY(50%);
    -o-transform:translateY(50%);
    transform:translateY(50%)
```

在下面添加：

``` css
.the-feature.from_left_and_right .info:hover h3{
    background:rgba(22, 22, 22, 0.85);
    letter-spacing: 0;
    text-shadow: none;
    font-size:17px;
}
```

在`.the-feature.from_left_and_right a:hover .info`里添加：

``` css
    background: rgba(0,0,0,0.6);
```

## 通告栏美化

和`startdash`一个问题，不够明显。

在上面添加了图标和`Notice`文字，稍稍加深了边框和文字颜色，微调位置。

在`\themes\sakura\source\css\style.css`中，找到`.notice`，修改以下属性：

``` css
    padding:17px;
    border:1px dashed #cacacaed;
    color:#707070;
    margin:-10px 0 -25px 0;
```

在`\themes\sakura\layout\index.ejs`中，将：

``` html
  <% if (theme.notice) { %>
  <div class="notice" style="margin-top:60px">
    <i class="iconfont icon-notification">
    </i>
    <div class="notice-content"><%= theme.notice%></div>
  </div>
  <% } %>
```

替换为：

``` html
  <% if (theme.notice) { %>
  <h1 class="main-title" style="font-family:'Ubuntu',sans-serif;margin-top:60px"> <i class="fa fa-commenting" aria-hidden="true"></i> Notice</h1>
  <div class="notice">
    <i class="iconfont icon-notification"></i>
    <div class="notice-content"><%= theme.notice%></div>
  </div>
  <% } %>
```

## 菜单图标美化

### 基础

推荐先了解一下FontAwesome是什么。

在[FontAwesome](http://www.fontawesome.com.cn/faicons/)里找到想要的图标，点进去，复制`fa-*`一项。

在主题配置文件中，找到`menu`。

每一栏有`path`和`fa`（有的有子菜单`submenus`），而`fa`就是对图标样式的控制。把刚才的`fa-*`替换掉里面的`fa-*`。

### 支持iconfont

[索引：iconfont是什么？怎么添加](/2019/09/18/iconfont/)

~~众所周知~~$sakura$主题配置文件的菜单图标只支持FontAwesome，由于种类太少需要添加`iconfont`支持。

其实就是很基础的`html`知识：

在`\themes\sakura\layout\_partial\header.ejs`中修改：

``` diff
- <i class="fa  <%= theme.menus[menu].fa %>" aria-hidden="true"></i>
+ <i class="<%= theme.menus[menu].fa %>"></i>
- <i class="fa <%= theme.menus[menu].submenus[submenu].fa %>" aria-hidden="true"></i>
+ <i class="<%= theme.menus[menu].submenus[submenu].fa %>"></i>
```

要注意的是原来用FontAwesome的图标要加上`fa`。

### 动态图标

鼠标放到菜单上的时候图标会有动画效果。实际上这是来自于[这个仓库](https://github.com/l-lin/font-awesome-animation)的特效。被放在了`\themes\sakura\source\css\lib.min.css`。

食用方法：

首先在[这里](https://l-lin.github.io/font-awesome-animation/)可以看到预览，找到需要的，把后面的`faa-*`粘到（原来有的话就替换）主题配置文件里`menu`->`fa`。

如果要改速度的话就再加上`faa-fast`/`faa-slow`。

这是你会发现`iconfont`并不支持动画。

解决方案非常简单，在`\thems\sakura\source\css\style.css`中添加：

``` css
.ctz{display: inline-block;}
```

（把`ctz`替换成你的`FontClass/Symbol前缀`）

最后我强迫症，修一个小问题：

有的时候把鼠标放到菜单某一栏上，下面的条已经出来了，但是图标并没有动。

在`\themes\sakura\layout\_partial\header.ejs`中，删除`<span class="faa-parent animated-hover">`和其对应的`</span>`，在它上面的`<a href="<%- url_for(theme.menus[menu].path) %>">`内部添加` class="faa-parent animated-hover"`。

## 板块化

这是目前对$sakura$效果最大的一次改动，直接改变了主题风格。

本来$sakura$在白色背景下文本都是和背景融为一体的，这次将文本主题、目录和评论区都加上了板块并添加了阴影。~~其实我本来只是想优化valine外观来着~~

emm...具体过程没法语言描述，只能靠f12研究了。

可能用得到的核心css：

``` css
.gather{
	width:830px;
	margin:0  auto;
	margin-bottom: 25px;
	box-shadow: 0px 0px 30px rgba(0,0,0,0.6);
	background:rgba(255,255,255,0.8) !important;
	border-radius:15px;
}
#vcomments{
	width:800px;
	padding:2.3% 1% 2% 1%;
	margin:0 auto;
}
```

---

# 杂七杂八

## 评论

### Valine输入框内容更改

在`\themes\sakura\layout\_partial\comment.ejs`中，修改`placeholder`一栏即可。

不过我在`\themes\sakura\source\js\sakura-app.js`中也找到了$Valine$的设置，不放心也改了。

找到以下内容：

``` javascript
    VA: function () {
      if (!valine) {
        var valine = new Valine()
        valine.init({
          el: '#vcomments',
          appId: mashiro_option.v_appId,
          appKey: mashiro_option.v_appKey,
          path: window.location.pathname,
          placeholder: '你是我一生只会遇见一次的惊喜 ...'
        })
      }
    },
```

同样修改`placeholder`。

### Valine外观美化

评论框的背景：在`\themes\sakura\source\css\style.css`中，修改`#veditor`中的`background-image`。

其他：

``` css
/* 输入昵称、邮箱下方虚线的颜色 */
.vinput:focus{border-bottom-color: #3ca0ff !important;}
/* 评论者的昵称颜色 */
.vnick:not(.vinput){color:#006eff!important;}
/* 鼠标悬浮时，评论者的昵称颜色 */
.vnick:not(.vinput):hover{color:#04f!important;}
/* 回复按钮的颜色*/
.vat{
	color:#3ca0ff !important;
	margin-right:3px;
	border:1px solid #3ca0ff;
	border-radius:4px;
	padding:0 0.5% 0 0.5%;
}
/* 评论框的边框 */
.vwrap{
	border:none !important;
	border-radius:12px !important;
	background:rgba(255, 255, 255, 0.3);
	box-shadow:0px 0px 18px #bbb;
	padding:14px;
	margin-top:10px;
}
```

### Valine炸了

最近$leancloud$爆炸了，导致$Valine$无法正常使用。然而我发现原来$next$评论是正常的，于是查阅了一下$next$的$Valine$配置，有了解决方案：

在`\themes\sakura\layout\_partial\footer.ejs`中，把：

``` html
<script src='//unpkg.com/valine@1.3.4/dist/Valine.min.js'></script>
```

替换为：

``` html
<script src='//unpkg.com/valine/dist/Valine.min.js'></script>
```

### 我不用Valine啦！（雾）

个人是很喜欢$Valine$的，不过现在$Valine$要实名认证很麻烦。

虽然最后我还是实名了，还是提供一个[sakura安装gitalk的教程](https://yremp.live/hexo-sakura-install-gitalk/)给弃了$Valine$的同志。

### Valine超进化——Volantis！

Volantis是基于Valine的升级版。

进化效果：

- 评论必须填写昵称和邮箱
- 可以自定义js（虽然valine也可以）
- 表情大翻新，甚至可以个性化表情

但是作者说即将关闭volantis的仓库，以后可能没有原版volantis的源码，所以这里直接提供我的`js`代码。

### 添加

如果你想直接照搬我的：

在`/themes/sakura/layout/_partial/footer.ejs`中，找到关于`Valine`的引用，长这个样子：

``` html
<script src="...valine..."></script>
```

替换为：

``` html
<script src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.1.4/js/volantis.min.js"></script>
```

如果你想DIY，下载 https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.1.4/js/volantis.min.js 并引用。

在`/themes/sakura/source/css/style.css`中，添加：

``` css
#v-qq{height:30px;}
#v-tieba{height:35px;}
#v-menhera:not(.v-avoid){max-width: 6.5%;}
#v-chino:not(.v-avoid){max-width: 8%;}
```

#### 表情强化

需要`js`基础。

因为`volantis.min.js`已经经过混淆处理，需要先用一些工具美化回去。

在2300多行，`e.exports`后面，格式：`表情名: 表情路径`。

你可以看到里面已经通过上面的函数添加了一些表情，照葫芦画瓢可以添加，比如`aru1: b(1),`。

如果想要更多表情（我添加了menhera酱和智乃酱的），需要自己写函数或直接用路径添加。

#### VolantisのDIY

会`js`的话直接改文件就好了。

比如我把昵称、邮箱、网站的框的内容修改了。

---

## 添加标签

### [标签页](/tags)

在`\themes\sakura\layout\_widget\common-page.ejs`中找到：

``` html
  <% if (post.photos && post.photos.length){ %>
    <div class="pattern-center ">
      <div class="pattern-attachment-img">
        <img src="<%- theme.lazyloadImg%>" data-src="<%= post.photos %>" class="lazyload" onerror="imgError(this,3)" style="width: 100%; height: 100%; object-fit: cover; pointer-events: none;">
      </div>
      <header class="pattern-header ">
        <h1 class="entry-title">
        <%= post.keywords %></h1>
      </header>
    </div>
  <% } %>
```

在下面添加：

``` html
  <% if (page.path === "tags/index.html"){ %>
<div class="site-content">
	<br><br>
	<div class="tag-cloud-title" style="text-align:center">
	  目前共计 <%=site.tags.length%> 个标签
	</div>
	<p></p>
    <div class="tags" style="text-align:center">
        <%- tagcloud({
            min_font: 14, 
            max_font: 35, 
            amount: 999, 
            color: true, 
            start_color: 'gray', 
            end_color: 'black',
        }) %>
    </div>
    <style>
        .article-entry ul li:before{
            display: none;
        }
        .article-inner  {
            text-align: center;
        }
        .tags {
            max-width: 40em;
            margin: 2em auto;
            margin-top: 0em;
        }
        .tags a {
            margin-right: 1em;
            border-bottom: 1px solid gray;
            line-height: 65px;
            white-space: nowrap;
        }
        .tags a:hover {
			transition: all 0.15s linear 0s;
			border-bottom-color: #07f !important;
            color: #07f !important;
        }
    </style>
	</div>
<% } %>
```
### 文章标签

注：文章标签有多个版本，且除非有特殊说明，每个版本的添加方式都是独立的，请先阅读完或直接跳到最后一项再添加。

#### v1.0：landfarzの朴素文字

$sakura$在文章界面是不显示标签的。

在`\themes\sakura\layout\_widget\common-article.ejs`中可以找到

``` html
<div class="post-tags"></div>
```

显然里面啥都没有。。。

如果去掉了页面底部版权信息，这句也被删掉了。没去掉的话就先删掉。

至于怎么添加，我直接十分暴力地魔改了[landfarz](https://github.com/wizardforcel/hexo-theme-landfarz)的代码过来。

在`common-article.ejs`中找到以下代码：

``` html
 <span id="busuanzi_value_page_pv"></span>次阅读</p>
```

下面添加：

``` html
<% if (post.tags && post.tags.length){ %>
      <%  var tags = [];  post.tags.forEach(function(tag){tags.push('<a href="' + config.root + tag.path + '">' + tag.name + '</a>');});%>
        <div class="tags">
		  <style scoped>
		    a{color: #fff;}
		  </style>
		  &nbsp<i class="fa fa-tag" aria-hidden="true"></i>&nbsp
	      <%- tags.join('&nbsp &nbsp') %>
        </div>
<% } %>
```

就可以了。

这是添加到顶部，放到下面的话就加进`<footer>`元素里。若删掉了`<footer>`再加回去就好了。

#### v2.0：来自butterflyの支持

看久了感觉只有文字的标签好$low$啊。。。

标签得有标签的亚子，怎么也得带个框啊。

尝试把[SPFK](https://dftyem.github.io/)的标签样式抄过来。。。

抄不动抄不动{% emoji_coda 2233/yumen %}

于是把[butterfly](https://stevebraveman.github.io/blog/)抄了过来：

在`\themes\sakura\source\css\style.css`中，添加：

``` css
.butterfly-tags{
	display: inline-block;
	width: fit-content;
	margin: 0.4rem 0.4rem 0.4rem 0;
	padding: 0rem 0.5rem;
	border: 1px solid #3187ff;
	border-radius: 1rem;
	background: #fff;
	color: #3187ff;
	text-decoration: none;
	font-size: 14px;
	text-shadow: none;
	transition: all 0.1s linear;
}
.butterfly-tags:hover{
	transition: all 0.1s linear;
	color: #fff;
	background: #3187ff;
}
```

按`v1.0`添加`html`代码，其中第二行`>`前添加` class="butterfly-tags"`，删除倒数第三行的`&nbsp &nbsp`。

#### v3.0：强♂迫SPFK支持

$butterfly$的样式我还是不满意，肝了一上午的尝试有了最终方案：

在`common-article.ejs`中找到以下代码：

``` html
 <span id="busuanzi_value_page_pv"></span>次阅读</p>
```

下面添加：

``` html
	  <% if (post.tags && post.tags.length){ %>
      <%  var tags = [];  post.tags.forEach(function(tag){tags.push('<li class="spfk-li"><a href="' + config.root + tag.path + '" class="butterfly-tags">' + tag.name + '</a></li>');});%>
        <div>
		  <ul class="spfk-ul">
		    &nbsp<i class="fa fa-tag" aria-hidden="true"></i>&nbsp&nbsp&nbsp&nbsp
	        <%- tags.join('') %>
          </ul>
		</div>
		<script>
			function colorInit(c,num){for(var i=1;i<=num;++i)c.push(i+'');}
			function paintTags(){
				var colorNumber=8,tagColors=[],tags=document.getElementsByClassName('butterfly-tags');
				for(var i = 0 ; i < tags.length ; ++i){
					if(!tagColors.length)colorInit(tagColors,colorNumber);
					var j=Math.floor(Math.random()*tagColors.length);
					tags[i].classList.add("spfk-color"+tagColors[j]);
					tagColors.splice(j,1);
				}
			}
			paintTags()
		</script>
    <% } %>
```

在`\themes\sakura\source\css\style.css`中，（如果有的话）删除`.butterfly-tags`的样式，添加：

``` css
.butterfly-tags{
	display: inline-block;
	margin: 0.4rem 1rem 0.4rem 0;
	padding: 0rem 0.45rem;
	border: 1px solid transparent;
	border-radius: 1px 6px 6px 1px;
	color: #fff;
	text-decoration: none;
	font-size: 13px;
	text-shadow: none;
	position: relative;
	transition: all 0.1s linear;
}
.butterfly-tags:hover{
	transition: all 0.1s linear;
	opacity: 0.85;
	color: inherit;
}
.butterfly-tags:focus{color: #fff;}
.butterfly-tags:before{
	content: " ";
	position: absolute;
	border: 11px solid transparent;
	left: -23px;
	top: -1px;
}
.butterfly-tags:after{
	content: " ";
    width: 4px;
    height: 4px;
    background-color: #fff;
    border-radius: 4px;
    -webkit-box-shadow: 0px 0px 0px 1px rgba(0,0,0,0.3);
    box-shadow: 0px 0px 0px 1px rgba(0,0,0,0.3);
    position: absolute;
	left: -2px;
	top: 8px;
}
.spfk-ul{
	float: left;
	padding: 0;
	margin: 0;
}
.spfk-li{display: inline-block;}
.spfk-color1{
	background: #39b3d7;
	border-color: #39b3d7;
}
.spfk-color1:before{border-right-color: #39b3d7;}
.spfk-color2{
	background: #4cae4c;
	border-color: #4cae4c;
}
.spfk-color2:before{border-right-color: #4cae4c;}
.spfk-color3{
	background: #f4a83c;
	border-color: #f4a83c;
}
.spfk-color3:before{border-right-color: #f4a83c;}
.spfk-color4{
	background: #ee6252;
	border-color: #ee6252;
}
.spfk-color4:before{border-right-color: #ee6252;}
.spfk-color5{
	background: #e23794;
	border-color: #e23794;
}
.spfk-color5:before{border-right-color: #e23794;}
.spfk-color6{
	background: #9537ff;
	border-color: #9537ff;
}
.spfk-color6:before{border-right-color: #9537ff;}
.spfk-color7{
	background: #0a5dff;
	border-color: #0a5dff;
}
.spfk-color7:before{border-right-color: #0a5dff;}
.spfk-color8{
	background: #cb3800;
	border-color: #cb3800;
}
.spfk-color8:before{border-right-color: #cb3800;}
```

~~可把我累坏了~~{% emoji_coda 2233/tuhun %}

同时发现了$SPFK$的$BUG$：

$SPFK$的三角形标签样式是通过伪元素`:before`实现的，而其位置的控制是通过`position:absolute`及`left`和`top`属性，这样在缩放页面时，三角形会脱离标签。更要命的是，在和我机房电脑屏幕比例不一样的电脑上，不需要缩放三角形就会自动脱离标签。

![](/images/blogの搭建之sakura-8.png)

#### v4.0：缩放BUG？不存在的！

肝了半个上午和半个下午把缩放$BUG$解决了。

在`common-article.ejs`中`<span id="busuanzi_value_page_pv"></span>次阅读</p>`下面添加：

``` html
	  <% if (post.tags && post.tags.length){ %>
      <%  var tags = [];  post.tags.forEach(function(tag){tags.push('<li class="spfk-li"><span class="triangle"></span><a href="' + config.root + tag.path + '" class="butterfly-tags">' + tag.name + '</a></li>');});%>
    <div>
		  <ul class="spfk-ul">
		    &nbsp<i class="fa fa-tag" aria-hidden="true"></i>&nbsp;<%- tags.join('') %>
          </ul>
		</div>
		<script>
			function colorInit(c,num){for(var i=1;i<=num;++i)c.push(i+'');}
			function paintTags(){
				var colorNumber=8,tagColors=[],tags=document.getElementsByClassName('spfk-li');
				for(var i = 0 ; i < tags.length ; ++i){
					if(!tagColors.length)colorInit(tagColors,colorNumber);
					var j=Math.floor(Math.random()*tagColors.length);
					tags[i].classList.add("spfk-color"+tagColors[j]);
					tagColors.splice(j,1);
        }
			}
			paintTags()
		</script>
    <% } %>
```

在`style.css`里，添加：

``` css
.butterfly-tags{
	display: inline-block;
	margin: 0.4rem 1rem 0.4rem 0;
	padding: 0rem 0.45rem;
	border: 1px solid transparent;
	border-radius: 1px 6px 6px 1px;
	color: #fff;
	text-decoration: none;
	font-size: 13px;
	text-shadow: none;
	position: relative;
    transition: all 0.1s linear;
    height:22px;
}
.spfk-li:hover{
	transition: all 0.1s linear;
	opacity: 0.85;
	color: inherit;
}
.butterfly-tags:hover{color: inherit;}
.butterfly-tags:focus{color: #fff;}
.butterfly-tags:after{
	content: " ";
    width: 4px;
    height: 4px;
    background-color: #fff;
    border-radius: 4px;
    -webkit-box-shadow: 0px 0px 0px 1px rgba(0,0,0,0.3);
    box-shadow: 0px 0px 0px 1px rgba(0,0,0,0.3);
    position: absolute;
	left: -2px;
	top: 8px;
}
.spfk-ul{
	float: left;
	padding: 0;
	margin: 0;
}
.triangle{
    border:11px solid transparent;
    display:inline-block;
    position:relative;
    top:6px;
}
.spfk-li{
    display: inline-block;
    margin-right:-17px;
    margin-left:-2px;
}
.spfk-color1 a{
	background: #39b3d7;
	border-color: #39b3d7;
}
.spfk-color1 span{border-right-color: #39b3d7;}
.spfk-color2 a{
	background: #4cae4c;
	border-color: #4cae4c;
}
.spfk-color2 span{border-right-color: #4cae4c;}
.spfk-color3 a{
	background: #f4a83c;
	border-color: #f4a83c;
}
.spfk-color3 span{border-right-color: #f4a83c;}
.spfk-color4 a{
	background: #ee6252;
	border-color: #ee6252;
}
.spfk-color4 span{border-right-color: #ee6252;}
.spfk-color5 a{
	background: #e23794;
	border-color: #e23794;
}
.spfk-color5 span{border-right-color: #e23794;}
.spfk-color6 a{
	background: #9537ff;
	border-color: #9537ff;
}
.spfk-color6 span{border-right-color: #9537ff;}
.spfk-color7 a{
	background: #0a5dff;
	border-color: #0a5dff;
}
.spfk-color7 span{border-right-color: #0a5dff;}
.spfk-color8 a{
	background: #cb3800;
	border-color: #cb3800;
}
.spfk-color8 span{border-right-color: #cb3800;}
```

这样三角形就不会脱离了。但是还是有一个小$BUG$：缩放时三角形的高度与方框不一致，导致轻微的上下错位。不过放大基本是看不出来的，至少得240%才会触发；而缩小虽然明显能观察到，但是$sakura$本身字体已经够小了，我想一般也很少有人会往小了缩吧。

---

## 归档页面优化

### 「全部展开/收缩」按钮优化

在`\themes\sakura\layout\archive.ejs`中，找到：

``` html
<p style="text-align:right;">
  [<span id="al_expand_collapse" style="cursor: s-resize;">全部展开/收缩</span>]</p>
```

修改为：

```html
<p style="text-align:left;">
  <button id="al_expand_collapse">
	<style>
	button#al_expand_collapse {
		color: white;
		border-radius: 16px;
		font-weight: bold;
		font-size: 15px;
		background-color: #ff9100;
		transition-duration: 0.3s;
		box-shadow: 0 3px 8px 0 rgba(0,0,0,0.11), 0 3px 8px 0 rgba(0,0,0,0.11);
        outline: none;
	}
	button#al_expand_collapse:hover {
        transition: all 0.2s linear 0s;
		background-color: #ff5900; 
	}
	</style>
	<i class="fa fa-bars" aria-hidden="true"></i> 全部展开/收缩
</button>
</p>
```
### 页首优化

浏览各个主页面的时候总觉得怪怪的，发现原来是归档页面没有配图。

然而归档是一个特殊页面，没有`md`文件也没法配图。

于是暴力修改`ejs`文件，顺便使其样式与其它页面相同：

在`\themes\sakura\layout\archive.ejs`中，在第一行：

``` html
<div class="blank" style="padding-top: 75px;">
</div>
```

修改为

``` html
<div class="pattern-center-blank" style="padding-top: 75px;">
</div>
<% if ( theme.archivesimg && theme.archivesimg.length ){ %>
    <div class="pattern-center-sakura">
      <div class="pattern-attachment-img">
        <img src="<%- theme.lazyloadImg%>" data-src="<%= theme.archivesimg %>" class="lazyload" onerror="imgError(this,3)" style="width: 100%; height: 100%; object-fit: cover; pointer-events: none;">
      </div>
	  <header class="pattern-header ">
        <h1 class="entry-title">
        归档</h1>
      </header>
    </div>
  <% } %>
```

删除：

``` html
    <header class="page-header">
      <h1 class="cat-title">
      归档</h1>
      <span class="cat-des">
        <p>
        Archives</p>
      </span>
    </header>
```

在主题配置文件中添加：

```
archivesimg:
```

后面填上图片路径，注意加空格。

### 标题修改

归档页面的标题一直叫`archive_a`，肥肠神奇。

在`\themes\sakura\languages\zh-cn.yml`开头添加：

```
archive_a: 归档
```
## 我不用不蒜子啦！

一直对不蒜子阅读量统计不满意，加载速度慢，经常卡住。

$Valine$同样有阅读量统计，而且$leancloud$的速度我是很信任的。

在`\themes\sakura\source\js\sakura-app.js`中，找到函数`valine.init`，在`appKey`一行下面添加：

``` diff
+ visitor: true,
```

在`\themes\sakura\layout\_partial\comment.ejs`中进行同样的修改。

删除：

``` diff
- $.getScript('//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js');
```

在`\themes\sakura\layout\_widget\common-article.ejs`中，替换（有两处）：

``` diff
- <span id="busuanzi_value_page_pv"></span>次阅读
+ <span id='/<%= page.path %>' class="leancloud-visitors" data-flag-title="<%= page.title %>"><a class="leancloud-visitors-count">1000000</a><a class="post-meta-item-text"> 次阅读</a></span>
```

在`\themes\sakura\layout\_partial\footer.ejs`中删除：

``` diff
- <script src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>
```

原来的阅读量丢失了？$Valine$阅读量统计以及评论有一个非常大的特点，就是随意更改。

到$leancloud$你创建的应用里，存储→Counter就是阅读量统计了。不过没啥好办法，只能一个一个改。

## 我不用Hexo啦！（雾）

[神秘の传送门](https://www.cnblogs.com/zouwangblog/p/11541835.html)

偶然间发现的，魔改博客园为$sakura$样式！而且还原度极高！

试了试水，[效果](https://www.cnblogs.com/ctz45562/)很惊艳。

当然我没有想法转到博客园上，链接挂在这给需要的同志，并膜拜+资瓷作者。{% emoji_coda 2233/diyi %}

## 加个游戏

> 我们不生产游戏，我们只是代码的搬运工。{% emoji_coda quyin/witty %}

步骤都是一样的（`name`代指游戏名）：

1.在`\source`中新建`name.html`，把链接里的内容粘进去。

注：我为了缩小`html`体积使用了[html美化工具](https://tool.lu/html/)压缩，如果想魔改需要美化回去。

2.在站点配置文件中，`skip_render`一栏添加：`name.html`。如果有多项按如下格式添加：

```
skip_render:
  - ...
  - ...
  - name.html
```

3.访问`博客地址/name.html`开始游戏（去掉`.html`后缀也行）

> 闲扯：本来是想直接挂一个下载链接的，但是不知道为啥我的blog的`<a>`标签无法用`download`属性转成下载链接，只好挂`github`链接。。。{% emoji_coda 2233/wuyan %}

### 2048

来源： 直接从[这位dalaoのblog](https://stevenmhy.tk)里搬来的。 

链接：[2048](https://github.com/ctz45562/blog-games/blob/master/2048.html)

说明：

原博客采用的$K-ON$风格，我魔改成了点兔的，同时也对`html`结构进行了少量修改。

若想修改图片（需要一些`html`、`css`的基础知识）在引入的`main.css`中能找到，懂的自然懂~{% emoji_coda xiaodianshi/xiao %}

### flappy bird

来源：https://github.com/sharpwind612/My-flappy-bird

链接：[flappy bird](https://github.com/ctz45562/blog-games/blob/master/flappybird.html)

### 五子棋

来源：http://kai.xlightgod.cf/

链接：[gobang](https://github.com/ctz45562/blog-games/blob/master/gobang.html)

### 生火间

来源：https://github.com/doublespeakgames/adarkroom

链接：[a dark room](https://github.com/ctz45562/blog-games/blob/master/adarkroom.html)

说明：

由于我剽的时候疏忽了，没有改`js`里的路径，恰好我的`git`无法`push`，更新不了`html`文件。生火间需要额外在`\themes\sakura\source\css`里新建`dark.css`，[内容戳这儿](https://github.com/ctz45562/cdn/blob/master/adarkroom/css/dark.css)。

### 水果忍者

来源：https://blog.csdn.net/aaa333qwe/article/details/72879188

链接：[fruit ninja](https://github.com/ctz45562/blog-games/blob/master/fruit-ninja.html)

## 模糊字体

从[这个博客](https://movetoex.github.io/)发现的。

就像这样：

戳这儿→ <span class="spoiler">被你发现了</span>

在`\themes\sakura\source\css\style.css`中，添加：

``` css
span.spoiler:hover {text-shadow: grey 0px 0px 4px;}
span.spoiler {
    color: rgba(0, 0, 0, 0);
    background-color: rgba(0, 0, 0, 0);
    text-shadow: grey 0px 0px 8px;
    cursor: pointer;
    -webkit-transition: text-shadow .5s ease;
    -moz-transition: text-shadow .5s ease;
    transition: text-shadow .5s ease;
}
span.spoiler.revealed {text-shadow: grey 0px 0px 0px;}
```

在`\themes\sakura\layout\_widget\common-article.js`中，添加：

``` html
<script>
var spoiler=document.getElementsByClassName('spoiler');
for(var i=0;i<spoiler.length;++i)
spoiler[i].onclick=function(){this.classList.toggle("revealed");};
</script>
```

按如下格式食用：

``` markdown
<span class="spoiler">文本</span>
```

## 文章信息添加「分类于」

在`\themes\sakura\layout\_widget\common-article.ejs`中，`<%= date(post.date, 'YYYY-M-D') %>`下面添加：

``` html
    <span class="bull"></span>
    <% if(post.categories && post.categories.length){ %>
      <span><i class="fa fa-archive"></i> 分类于  
        <% post.categories.each((category)=>{ %><span class="article-category"><a href="<%- url_for(category.path) %>" > <%= category.name %></a></span>
      <% }) %>
    </span>
    <% } %>
```

在`\themes\sakura\source\css\style.css`中，添加：
``` css
.article-category{
    display: inline-block;
    line-height: 0 !important;
    margin: 0;
    padding: 0.55rem 0.2rem;
    border: 1px solid #0a87ff;
    background: rgba(255, 255, 255, 0.93);
    color: #0a87ff !important;
    text-decoration: none;
    font-size: 13px;
    text-shadow: none;
    box-shadow: 0 0 6px #111;
    transition: all 0.1s linear;
    border-radius:10%;
}
.article-category:hover{
    transition: all 0.1s linear;
    color: #fff !important;
    background: #0a89ffec;
}
.article-category a{color:inherit !important;}
```

## 施工中...

~~闲的蛋疼加了这么一个功能~~

如果某篇文章没有完成~~不传上去不就行了~~，在`front-matter`里加上一句`incomplete: true`，文章底部就会酱紫：

<blockquote><center><img src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.3/emojis/gif/23.gif"></center><p style="font-size:32px;text-align:center;margin-top:-2px">施工中...</p></blockquote>

<span class="spoiler">不要问我为什么是哔咔娘</span>

在`\themes\sakura\layout\_widget\common-article.ejs`中，`<%- post.content %>`下面添加：

``` html
<% if(post.incomplete&&post.incomplete==true) {%>
<hr>
<center><img class="lazyload" onerror="imgError(this,3)" src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/lazyload.gif" data-src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.3/emojis/gif/23.gif"></center>
<p style="font-size:32px;text-align:center;margin-top:-2px">施工中...</p>
<% } %>  
```

---

# 瞎搞

记录一下瞎删掉的东西，要是有了$BUG$还原一下。

1.在`\themes\sakura\source\js\sakura-app.js`中，`$.getScript('//cdn.jsdelivr.net/vue/latest/vue.min.js', function () {`一行下面，删除了：

``` javascript
	$.getScript('//unpkg.com/botui/build/botui.min.js', function () {
		bot_ui_ini()
	})
```

2.在`\themes\sakura\layout\_widget\common-article.ejs`中删除了：

``` html
<div class="toc" style="background: none;"></div>
```

3.在`\themes\sakura\source\css\style.css`中，删除了：

``` css
button:hover,input[type="button"]:hover,input[type="reset"]:hover,input[type="submit"]:hover {
    /*border-color:#ccc #bbb #aaa;*/
    /*box-shadow:inset 0 1px 0 rgba(255,255,255,.8),inset 0 15px 17px rgba(255,255,255,.8),inset 0 -5px 12px rgba(0,0,0,.02)*/
}
button:focus,input[type="button"]:focus,input[type="reset"]:focus,input[type="submit"]:focus,button:active,input[type="button"]:active,input[type="reset"]:active,input[type="submit"]:active {
    /*border-color:#aaa #bbb #bbb;*/
    /*box-shadow:inset 0 -1px 0 rgba(255,255,255,.5),inset 0 2px 5px rgba(0,0,0,.15)*/
}
```

``` css
#bgvideo {
    position:absolute;
    top:0;
    left:0;
    margin:0;
    padding:0;
    min-width:99.999%;
    min-height:550px;
    z-index:0
}
#video-btn,#video-add {
    position:absolute;
    bottom:3px;
    right:5px;
    width:32px;
    height:32px;
    z-index:7;
    background-position:center;
    background-size:cover;
    cursor:pointer;
    opacity:.8;
    -moz-animation:poi-face 10s linear infinite alternate;
    -webkit-animation:poi-face 10s linear infinite alternate;
    -o-animation:poi-face 10s linear infinite alternate;
    animation:poi-face 10s linear infinite alternate
}
#video-btn:hover,#video-add:hover {
    opacity:1
}
.video-play,.loadvideo {
    background-image:url('https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/other/play@32x32.png')
}
.video-pause {
    background-image:url('https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/other/pause@32x32.png')
}
#video-add {
    background-image:url('https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/other/add@32x32.png');
    bottom:45px;
    display:none
}
.video-stu {
    position:absolute;
    bottom:-100px;
    left:0;
    right:0;
    margin:auto;
    padding:6px 15px;
    text-align:center;
    color:#fff;
    width:100%;
    background-color:rgba(0,0,0,.8);
    border-radius:0;
    font-size:18px;
    -webkit-transition:.4s ease all;
    -moz-transition:.4s ease all;
    -o-transition:.4s ease all;
    transition:.4s ease all
}
```

``` css
  cursor:url(https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/cursor/No_Disponible.cur),auto;
```

``` css
  cursor:url(https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/cursor/normal.cur),auto
```

``` css
  cursor:url(https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/cursor/texto.cur),auto
```

4.`\themes\sakura\source\css\style.css`：

``` diff
- .logolink a:hover .sakurasono {
-     background-color:orange;
-     color:#fff
- }
```

`.logolink .sakurasono`：

``` diff
- background-color:rgba(255,255,255,.5);
```

在`.logolink a:hover .shironeko,.logolink a:hover rt `前添加了：`.logolink a:hover .sakurasono,`

5.`\themes\sakura\layout\_partial\head.ejs`：

``` diff
- <link rel="stylesheet" type="text/css" href="/css/sharejs.css">
- <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Noto+SerifMerriweather|Merriweather+Sans|Source+Code+Pro|Ubuntu:400,700|Noto+Serif+SC" media="all">
- <link rel="stylesheet" href="/css/lib.min.css" media="all">\
+ <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.1/css/googlefont.css" media="all">
+ <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.1/css/lib.min.css" media="all">
```

6.`\themes\sakura\layout\_partial\footer.ejs`：

``` diff
- <script src="https://cdn.jsdelivr.net/npm/clipboard@2/dist/clipboard.min.js"></script>
+ <script src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.2/js/clipboard.min.js"></script>
```

7.在`\themes\sakura\source\js\sakura-app.js`中，修改了：

``` diff
- $.getScript('//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js');
+ $.getScript('//cdn.jsdelivr.net/gh/ctz45562/cdn@1.4.0/js/busuanzi.pure.mini.js');
```

8.在`\themes\sakura\layout\_widget\common-page.ejs`倒数第二行：

``` diff
- </div>
+ </article>
```

9.在`style.css`中：

```diff
#page {
-    min-height: calc(100vh - 150px);
+    min-height: calc(100vh - 45562px); 
}
```

10.在`common-article`中：

``` diff
- <link rel="stylesheet" type="text/css" href="/css/sharejs.css">
- <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/tocbot/4.4.2/tocbot.css">
+ <link rel="stylesheet" href="https://cdn.bootcss.com/tocbot/4.8.0/tocbot.css">
```

11.在`\_partial\footer.ejs`中：

``` diff
- <img src="https://cdn.jsdelivr.net/gh/honjun/cdn@1.6/img/other/disqus-preloader.svg">
```

12.在`\layout\_widget\index-items.ejs`中：

``` diff
- <div class="float-content"></div>
```

13.在`\source\css\style.css`中：

``` diff
@media(max-width:800px) {
    .changeSkin-gear span::before {
    content:""
}
.changeSkin i {font-size:20px}
.changeSkin-gear i {font-size:20px}
}
- position:relative;
-    z-index:99;
-    cursor:pointer
- }
```

14.删除了关于主题配置文件中的`prefixName`。

在`\layout\_partial\header.ejs`中：

``` diff
-            <span class="sakurasono"><%= theme.prefixName %></span>
-            <span class="shironeko"><%= theme.siteName %></span>
+            <span class="sakurasono"><%= theme.siteName %></span>
```

在`\source\css\style.css`中，删除了所有`.shironeko`。``

---

<center><div style="font-size:45px; color:#08f">To be continued...</div></center>
