---
title: K-D Tree学习笔记
date: 2019-12-17 20:20:52
categories: 学习笔记
tags:
    - 数据结构
    - K-D Tree
keywords: K-D Tree
description: 感jo kdt要写的东西比较多，干脆新开一篇了。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.0/kdtreexxbj.jpg
mathjax: true
---

感jo $kdt$ 要写的东西比较多，干脆新开一篇了。

<!--more-->

---

# 前言

学$kdt$前：$kdt$是一种用于维护多维点集的高级数据结构。

学$kdt$后：$kdt$就是加了些剪枝的暴力，看起来复杂度没保证，用于骗分。

---

# 抄袭来源

> 翻出来的pdf课件
> 
> https://oi-wiki.org/ds/kdt/

---

# #define

`son[x][0/1]/son(x,0/1)/ls(x),rs(x)`：$x$的左右儿子。

`xl[x],xr[x],yl[x],yr[x]`：二维$kdt$点$x$维护的矩形范围。

`d[x]`：点$x$的比较维度。

`pool`：内存池。`pool[0]`存储数量。

`sqr(x)`：`x*x`

---

# 修改操作

$kdt$是一棵$BST$。每个节点代表坐标系上的一个点，并维护一个$k$维立方体，其子树的点都被包含在该$k$维立方体内，且左右儿子的$k$维立方体的并等于该$k$维立方体。

代码都以$2$维$kdt$为例。

## 建树

选择一个维度（即`d[x]`），取点集中该维度的中位数作为自己的点，左边的点扔到`ls`里，右边的点扔到`rs`里，递归左右儿子，最后更新$k$维立方体范围。

这样造出来的$BST$树高是$\log$的。取中位数时用`nth_element`，复杂度是$O(n\log n)$的。

维度可以$1,2,...$循环选取，也可以`rand()`一个不易被卡。

``` cpp
int son[maxn][2],xl[maxn]={inf},xr[maxn]={-inf},yl[maxn]={inf},yr[maxn]={-inf},d[maxn],pool[maxn],siz[maxn],D;
struct point{
    int pos[2];
    bool operator < (const point &x)const{return pos[D]<x.pos[D];}
}poi[maxn],p[maxn];
inline void update(int node){
    xl[node]=min(min(xl[ls(node)],xl[rs(node)]),p[node].pos[0]),xr[node]=max(max(xr[ls(node)],xr[rs(node)]),p[node].pos[0]);
    yl[node]=min(min(yl[ls(node)],yl[rs(node)]),p[node].pos[1]),yr[node]=max(max(yr[ls(node)],yr[rs(node)]),p[node].pos[1]);
    siz[node]=siz[ls(node)]+siz[rs(node)]+1;
}
void build(int l,int r,int &node){
    if(l>r)return;
    int mid=l+r>>1;
    node=pool[pool[0]--],d[i]=D=rand()&1;
    nth_element(poi+l,poi+mid,poi+1+r);
    p[node]=poi[mid];
    build(l,mid-1,ls(node));
    build(mid+1,r,rs(node));
    update(node);
}
```

## 插入

以每个节点的$d[x]$比较和$BST$一样插入。

面临一个同样的问题是树会不平衡。

一种解决方法是把操作离线下来提前建树，未被插入的节点打上标记。执行插入时就找到它并激活。

但如果强制在线的话，就要考虑$BST$维护平衡的方式。旋转是不可能的了，每一个点的比较方式不一样怎么转？于是要用替罪羊树的方式重构$kdt$。

``` cpp
const double alpha = 0.7;
int cnt,root;
void insert(int &node,point P){
    if(!node){
        node=pool[pool[0]--];
        xl[node]=xr[node]=P.pos[0];
        yl[node]=yr[node]=P.pos[1];
        p[node]=P,siz[node]=1,d[node]=rand()&1;
        return;
    }
    insert(son(node,P.pos[d[node]]>p[node][d[node]]),P),++siz[node];
}
void clear(int &node){
    if(!node)return;
    clear(ls(node)),clear(rs(node));
    pool[++pool[0]]=node,poi[++cnt]=(point){p[node][0],p[node][1]},node=0;
}
void check(int &node,point P){
    if(!node)return;
    if(1.0*max(siz[ls(node)],siz[rs(node)])>=alpha*siz[node]){
        cnt=0,clear(node),build(1,cnt,node);
        return;
    }
    check(son(node,P.pos[d[node]]>p[node][d[node]]),P);
    update(node);
}
void ins(point P){
    insert(root,P),check(root,P);
}
```

## 删除

和替罪羊一样删，打上删除标记，重构的时候丢掉。

代码就不放了。

---

# 应用

前面看起来都很正常（都是基于平衡树的操作嘛），真正的暴力在下面。

$kdt$的查询方法是——**在遍历整棵树为前提下，通过计算极端值剪枝来少遍历一些点。**


## 平面最近/远点对

计算查询点到矩形域的最小/大距离，若最小/大值都大于/小于当前答案则返回。

好像会被构造数据卡。

### 欧几里得距离

$dis_{\min}=sqr(\max\{\max\{xl[node]-x,x-xr[node]\},0\})+sqr(\max\{\max\{yl[node]-y,y-yr[node]\},0\})$

$dis_{\max}=sqr(\max\{x-xl[node],xr[node]-x\})+sqr(\max\{y-yl[node],yr[node]-y\})$

### 曼哈顿距离

$dis_{\min}=\max\{\min\{xl[node]-x,x-xr[node]\},0\}+\max\{\min\{yl[node]-y,y-yr[node]\},0\}$

$dis_{\max}=\max\{xr[node]-x,x-xl[node]\}+\max\{yr[node]-y,y-yl[node]\}$

### 切比雪夫距离

转成曼哈顿距离就好了。

什么？不会转？~~5分钟前我也不会~~

- 切比雪夫转曼哈顿：$(x,y)\rightarrow(\frac{x+y}{2},\frac{x-y}{2})$
- 曼哈顿转切比雪夫：$(x,y)\rightarrow(x+y,x-y)$

### 代码

以欧几里得距离$\min$为例。还有一个小剪枝是先访问极端距离更小的儿子。

``` cpp
long long ans=inf;
int x,y;
inline long long sqr(int x){return 1ll*x*x;}
inline long long calc(int node){
    return node?sqr(max(max(xl[node]-x,x-xr[node]),0))+sqr(max(max(yl[node]-y,y-yr[node]),0)):inf;
}
void ask(int node){
    ans=min(ans,sqr(p[node].pos[0]-x)+sqr(p[node].pos[1]-y));
    int d[2]={calc(ls(node)),calc(rs(node))},t=d[1]<d[0];
    if(d[t]<ans)ask(son(node,t));
    if(d[t^1]<ans)ask(son(node,t^1));
}
```

## 矩形求和

单点加，矩形求和。

- 如果当前矩形与查询矩形完全不相交直接返回
- 如果当前矩形完全被包含在查询矩形内，返回矩形和
- 否则判断该节点代表的点是否在矩形内部，再递归左右儿子。

``` cpp
int x1,y1,x2,y2;
int ask(int node){
    if(xl[node]>x2||xr[node]<x1||yl[node]>y2||yr[node]<y1)return 0;
    if(xl[node]>=x1&&xr[node]<=x2&&yl[node]>=y1&&yr[node]<=y2)return sum[node];
    return ask(ls(node))+ask(rs(node))+(p[node].pos[0]>=x1&&p[node].pos[0]<=x2&&p[node].pos[1]>=y1&&p[node].pos[1]<=y2)?a[node]:0;
}
```

随机数据下单次查询$O(\log n)$，构造数据下为$O(\sqrt{n})$。

## 解决偏序问题

$kdt$维护$k$维偏序复杂度是$O(n^{\frac{k-1}{k}})$的。

强制在线用不了cdq分治，$kdt$可以用$O(kn)$的空间代替树套树，而且在非构造数据下效率很高。

---

# 水题

## 板子们

[平面最近欧几里得距离点对](https://www.luogu.com.cn/problem/P1429)

[平面最近/远曼哈顿距离点对](https://www.luogu.com.cn/problem/P2479)

[带修最近曼哈顿距离](https://www.luogu.com.cn/problem/P4169)

[只能kdt做的单点加矩形和](https://www.luogu.com.cn/problem/P4148)

[可以树套树和cdq做的单点加矩形和](https://www.luogu.com.cn/problem/P4390)

## [K远点对](https://www.luogu.com.cn/problem/P4357)

维护一个小根堆。如果当前点对的距离比堆顶大就替换掉堆顶。通过判断极端值和堆顶的关系选择是否递归。

但如果堆中元素不满$k$个就没得选择。

求的是无序点对，所以$k$要乘$2$。

[双倍经验](https://www.luogu.com.cn/problem/P2093)

## [巧克力王国](https://www.luogu.com.cn/problem/P4475)

这道题进一步加深了我对$kdt$暴力的理解。

直接暴力查，把$a,b$带进去计算极端值。最大值小于$c$返回矩形和，最小值大于等于$c$不递归。

要注意的是$a,b,x,y$都可能是负数，不一定是左上角和右下角取到极端值，四个端点都可能取到。

~~复杂度？kdt的题算什么复杂度~~

## [TATT](https://www.luogu.com.cn/problem/P3769)

$DP$方程显然：$f(i)=\max\limits_{a_j\le a_i,b_j\le b_i,c_j\le c_i,d_j\le d_i}\{f(j)\}+1$

第一维排序，$3$维$kdt$优化$DP$即可。

不想写带插入$kdt$，提前建好树，记录下原序列中每个点在$kdt$中的节点编号以及父节点，更新时直接跳父节点就好。

有个细节，第一维相同时要按其他维为第二关键字比较大小。
