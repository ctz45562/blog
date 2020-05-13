---
title: LCT重学笔记
date: 2020-03-24 08:31:13
categories: 学习笔记
tags:
    - 数据结构
    - LCT
keywords: LCT
description: 时隔半年再次来学习LCT
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.0.6/lctcxbj.jpg
mathjax: true
---

时隔半年再次来学习$LCT$，上次学是写[紫荆花之恋](/2019/09/07/%E6%B4%9B%E8%B0%B7-P3920-WC2014-%E7%B4%AB%E8%8D%86%E8%8A%B1%E4%B9%8B%E6%81%8B/)的时候了。

本来还想顺便写个$splay$重学笔记的，然而[维护数列](https://www.luogu.com.cn/problem/P2042)调了一上午，从TLE到WA一直10分自闭了。{% emoji_coda quyin/hematemesis %}

<!--more-->

---

# #define

`son[x][0/1]`，`son(x,0/1)`，`ls(x)/rs(x)`：点$x$在$splay$中的左右儿子

`fa[x]`：爹

`rev[x]`：翻转标记

`whson(x)`：判断$x$是左儿子还是右儿子

`root(x)`：判断$x$是否为所在$splay$的根

注意区分“$splay$的根”和“原树的根”。

---

# LCT

[板子](https://www.luogu.com.cn/problem/P3690)

## 序

$LCT$（$Link-Cut-Tree$）是一种动态维护树/森林的数据结构。

和树链剖分类似，把一棵树划分成若干条**实链**。把实链上的边称为**实边**，不在链上的边称为**虚边**。

但不同于树链剖分，实链是不断变化的。

每条实链用一棵$splay$维护，以深度为基准排序。

至于虚边，有“认父不认子”。

对于虚边$(x,y)$（$x$为深度较大的），$z$为$x$所在$splay$的根，则有$fa[z]=y$。但是$ls(y)$和$rs(y)$都不是$z$。

## 基本操作

tips：

- 根据上面的“认父不认子”，判断是否为$splay$的根就不能用$fa$了，而是以儿子情况判断。
- 旋转时，有一步是和祖父连边。在普通$splay$中，如果`fa[x]`已经是根了，祖父就是$0$。但由于虚边的存在，祖父不一定是$0$，需要特判。
- $LCT$里的$splay$函数是直接把$x$转成当前$splay$的根。
- 众所周知，$splay$前要下放标记，由于这里不找第$k$大，所以要额外写个函数放标记，递归或者栈模拟都可以。

``` cpp
#define son(x,y) son[x][y]
#define ls(x) son(x,0)
#define rs(x) son(x,1)
#define whson(x) (son(fa[x],1)==x)
#define root(x) (ls(fa[x])!=x&&rs(fa[x])!=x)
int sum[maxn],son[maxn][2],fa[maxn],rev[maxn],dat[maxn];
inline void update(int node){sum[node]=sum[ls(node)]^sum[rs(node)]^dat[node];}
inline void rever(int node){rev[node]^=1,swap(ls(node),rs(node));}//翻转
inline void pushdown(int node){
	if(rev[node]){
		if(ls(node))rever(ls(node));
		if(rs(node))rever(rs(node));
		rev[node]=0;
	}
}//下放标记
inline void adde(int s,int f,int wh){
	if(s)fa[s]=f;
	son(f,wh)=s;
}
inline void rotate(int x){
	int f=fa[x],gf=fa[f],wh=whson(x);
	fa[x]=gf;
	if(!root(f))son(gf,whson(f))=x;
	adde(son(x,wh^1),f,wh);
	adde(f,x,wh^1);
	update(f),update(x);
}//旋转
inline void pushtag(int node){
	if(!root(node))pushtag(fa[node]);
	pushdown(node);
}//下放整条链上的标记
void splay(int x){
	pushtag(x);
	int y;
	while(!root(x)){
		y=fa[x];
		if(!root(y))rotate(whson(x)^whson(y)?x:y);
		rotate(x);
	}
}
```

## access

<span class="spoiler">ctz来教你读英语：ě kē sài sì。</span>

<span class="spoiler">标准读音是/'ækses/</span>

顾名思义，$access$是打通一条从树根到$x$的实链，并把多余的实边变为虚边。

先放代码：

``` cpp
void access(int x){
	for(register int y=0;x;y=x,x=fa[x])
		splay(x),rs(x)=y,update(x);
}
```

把$x\ splay$上去，因为这条实链只能到$x$，所以把深度比$x$大的砍掉，`rs(x)=y`（此时$y=0$）。因为儿子变了所以要`update`一下。

因为接下来要把树根到$x$的$splay$串成一个，`y=x`用于记录当前$splay$的根节点，`x=fa[x]`跳到上面的$splay$上（$x$是$splay$的根所以一定是跳的虚边）。

此时我们要把虚边$(x,y)$变实，把$x\ splay$上去，深度比$x$大的砍掉，换成实边$(x,y)$，`rs(x)=y,update(x)`。

不断重复这一过程直到树根，就能完成`access`。

## makeroot

有了`access`一切都变得简单了。

把$x$定为树根。

`access(x)`打通实链，$splay(x)$把$x$转上去。此时$x$为所在$splay$中深度最大的，直接把$x$翻转成深度最小的即可。

``` cpp
void makeroot(int x){
	access(x),splay(x),rever(x);
}
```

## findroot

获取$x$所在的树的树根。

`access(x)`打通实链，此时$x$和树根在同一个$splay$中，而树根又是深度最小的，`splay(x)`后一直走左儿子就有了。

~~秉着splay要没事转一转的原则~~，找到根后把它转上来。

``` cpp
int findroot(int x){
	access(x),splay(x);
	while(ls(x))pushdown(x),x=ls(x);
	splay(x);
	return x;
}
```

## link

连边$(x,y)$。

直接连一条虚边就好啦。

为了保证连之前$x$上头没有点（$fa[x]=0$），先`makeroot(x)`一下。

至于判断是否合法，`findroot(y)`看看树根是否为$x$检查连通性即可。

``` cpp
void link(int x,int y){
	makeroot(x);
	if(findroot(y)==x)return;
	fa[x]=y;
}
```

## cut

先把$x,y$放到同一棵$splay$里。`makeroot(x),access(y)`即可。（由于`access(y)`在后面`findroot(y)`里出现了所以代码里不用写）

然后判断是否合法。首先`findroot(y)`检查连通性。由于在`findroot`时，经历了这样一个流程：`access(y),splay(y)`$\rightarrow$找到树根$x\rightarrow$`splay(x)`。所以如果$x,y$之间有边，一定是$x$为$splay$的根，$y$为$x$的右儿子，且没有深度介于$x,y$的点（$ls(y)=0$）。

最后如果合法，直接切断$x,y$的父子关系并辅以`update`。

``` cpp
void cut(int x,int y){
	makeroot(x);
	if(findroot(y)!=x||fa[y]!=x||ls(y))return;
	rs(x)=fa[y]=0;
	update(x);
}
```

## split

提取出链$x,y$以获得信息。

`makeroot(x),access(y)`打通实链$(x,y)$，然后`splay(y)`上去，$y$里面就存有整条链的信息。

---

# 其他

## 复杂度

世界上最无聊的复杂度分析之一就是$LCT$。

单次操作均摊复杂度$O(\log n)$，以及一个巨大的常数。

## 维护边权

转边为点即可。

## 维护子树

上面只提到了维护链的信息，实际上$LCT$弱于维护子树。

当然也不是完全没办法。以求子树大小为例，额外维护一个`lsiz`表示虚子树大小。在虚实边转换的时候计算贡献就好了。

一些函数的修改：

``` cpp
inline void update(int node){
    siz[node]=siz[ls(node)]+siz[rs(node)]+lsiz[node]+1;
}
void access(int node){
    for(register int y=0;x;y=x,x=fa[x])
        splay(x),lsiz[x]+=siz[rs(x)]-siz[y],rs(x)=y,update(x);
}
void link(int x,int y){
    makeroot(x);
	if(findroot(y)==x)return;
	fa[x]=y,lsiz[y]+=siz[x],update(y);
}
```

而维护最大值就得套个`set`了，复杂度又多了个$\log$。

---

# 水题

[现在已经不会了的题](/tags/LCT/)

## [由乃的OJ](https://www.luogu.com.cn/problem/P5354)

和[起床困难综合征](https://www.luogu.com.cn/problem/P2114)完全一致，维护全$0$和全$1$经过链变成什么。从高位到低位贪心，能填$0$就填$0$。

至于怎么维护，用$z_x$和$o_x$分别表示全$0$和全$1$经过$x$子树代表的链后的结果（从左子树走到右子树）。先把左儿子和$x$合并起来，分别记为$lz,lo$。再用$\&$分别提取出$0$位和$1$位，和右儿子合并。

$z_x=((\sim lz)\&z_{rs(x)})|(lz\&o_{rs(x)})$

$o_x=((\sim lo)\&z_{rs(x)})|(lo\&o_{rs(x)})$

因为要翻转，还要维护从右子树走到左子树的结果。

## [在美妙的数学王国中畅游](https://www.luogu.com.cn/problem/P4546)

[题解](/2020/03/28/洛谷-P4546-THUWC2017-在美妙的数学王国中畅游/)