---
title: cdq分治学习笔记
date: 2019-08-15 20:49:52
categories: 学习笔记
tags:
	- cdq分治
keywords: cdq分治
description: 点分治题给调吐了，于是来学cdq分治了（雾
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/cdqfzxxbj.jpg
mathjax: true
---

点分治题给调吐了，于是来学$cdq$分治了（雾

<!--more-->

---

# 前言

$cdq$分治是我第一个**正规**学习的离线算法。

$cdq$分治用于解决偏序问题，可以代替高级数据结构。

~~高级数据结构也可以代替cdq分治~~

---

# 抄袭来源

> E:\c\c++\学习资料\sls讲课课件\CDQ分治.pptx

---

# 偏序问题

## 实现

[栗子](https://www.luogu.org/problem/P3810)

~~选择好想又好写的树套树~~

典型的三维偏序。

思考归并排序求逆序对，本质上是二维偏序。

第一维位置已经排好了，一定是左边对右边产生贡献。

当分治区间左半边和右半边都分别按第二维权值排好序时，我们只要用两个指针扫一遍即可。

现在是三维偏序，同样第一维排序，对第二维归并排序，考虑上第三维套个树状数组。

左指针移动时$c$位置上加$1$，右指针移动时查询小于等于$c$的数。

这就是$cdq$分治了。

甚至可以把树状数组换成$cdq$，$cdq$套$cdq$。

栗子里面由于都是小于**等于**，而$cdq$分治里限制了只有左边对右边会产生贡献，$a,b,c$都相同的元素之间贡献算不上。

将序列去重，每个元素记录个数，处理$c$的影响时要加上它的个数，统计答案时也要加上个数。

## 代码

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 100005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
template<typename T>
inline T read(){
	T x=0;
	int y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int dat[maxn<<1],f[maxn],g[maxn],ans[maxn],n,m;
struct point{
	int a,b,c,v;
	int *ans;
	bool operator < (const point &x)const{
		if(a!=x.a)return a<x.a;
		if(b!=x.b)return b<x.b;
		return c<x.c;
	}
	bool operator == (const point &x){
		return a==x.a&&b==x.b&&c==x.c;
	}
}a[maxn],b[maxn];
#define lowb(x) (x&-x)
void add(int x,int d){
	while(x<=m)dat[x]+=d,x+=lowb(x);
}
int ask(int x){
	int ans=0;
	while(x)ans+=dat[x],x-=lowb(x);
	return ans;
}
void cdq(int l=1,int r=n){
	if(l==r)return;
	int mid=l+r>>1,k=l,pl=l,pr=mid+1;
	cdq(l,mid),cdq(mid+1,r);
	while(pl<=mid&&pr<=r){
		if(a[pl].b<=a[pr].b)add(a[pl].c,a[pl].v),b[k++]=a[pl++];
		else *a[pr].ans+=ask(a[pr].c),b[k++]=a[pr++];
	}
	while(pr<=r)*a[pr].ans+=ask(a[pr].c),b[k++]=a[pr++];
	for(register int i=l;i<pl;++i)add(a[i].c,-a[i].v);
	while(pl<=mid)b[k++]=a[pl++];
	for(register int i=l;i<=r;++i)a[i]=b[i];
}
int main(){
	n=read(),m=read();
	for(register int i=1;i<=n;++i)a[i].a=read(),a[i].b=read(),a[i].c=read(),a[i].v=1;
	sort(a+1,a+1+n);
	int len=0,rec=n;
	for(register int i=1;i<=n;i+=a[i].v){
		int k=i;
		while(k<n&&a[i]==a[k+1])++a[i].v,++k;
		++len,a[i].ans=f+len,b[len]=a[i],f[len]=(g[len]=a[i].v)-1;
	}
	n=len;
	for(register int i=1;i<=len;++i)a[i]=b[i];
	cdq();
	for(register int i=1;i<=n;++i)ans[f[i]]+=g[i];
	for(register int i=0;i<rec;++i)printf("%d\n",ans[i]);
}
```

## 复杂度

非常显然的$O(n\log^2 n)$。

$cdq$分治就跟线段树建树一个过程，最多$\log n$层，每层合计要$O(n\log n)$扫描，总复杂度$O(n\log^2 n)$，常数还很小。

---

# 优化DP

## 证明（并不是）

$\because$ 数据结构可以优化$DP$

又$\because cdq$分治可以代替高级数据结构

$\therefore cdq$分治可以优化$DP$

## 实现

[栗子](https://www.luogu.org/problem/P2487)

概括题意：

长度为$n$的序列，每个元素有两个属性$a,b$，求最长的子序列的长度，满足每一项的$a,b$都小于等于前一项。并求出每个元素出现在最长子序列中的概率。

设$f(i)$为以$i$为结尾的最长序列长度，$fc(i)$为序列个数，$g(i)$为以$i$为开头的最长序列长度，$gc(i)$类似于$fc(i)$。

设$ans=\max\{f(i)\},cnt=\sum\limits_{i=1}^n[f(i)=ans]fc(i)$。

对于每个元素$i$，若$f(i)+g(i)-1=ans$，则其概率为$\dfrac{fc(i)\*gc(i)}{cnt}$，否则为$0$。

方程跟$LIS$的差不多：

$f(i)=\max\limits_{j=1}^{i-1}\{f(j)+1\}(a_j\ge a_i,b_j\ge b_i)$

$fc(i)=\sum\limits_{j=1}^{i-1}[f(i)=f(j)+1]fc(j)(a_j\ge a_i,b_j\ge b_i)$

$g$和$gc$类似。

裸的树套树优化$DP$，空间$O(n\log^2 n)$，常数和码量也不小~~（但是好想）~~

观察方程，实际上转移的条件为$j<i,a_j\ge a_i,b_j\ge b_i$。

这不三维偏序吗。

假设分治区间为$[l,r]$，已经处理好$[l,mid]$的$f,fc$值，对$a$执行类似于归并排序的过程，再用树状数组维护一下前/后缀$DP$值（$b$需要离散化），大致和三维偏序一样。

为了使$DP$值是准确的，要先递归左区间，计算左区间对右区间的贡献，再递归右区间。

$g$和$gc$同样$cdq$分治。

时间复杂度$O(n\log^2 n)$。

## 代码

很鬼畜的是这题$fc$和$gc$的乘积会爆$long\ long$，得用$double$存。。。

代码是$yy$出来的，很丑常数也不够优秀。

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 50005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
#define lowb(x) (x&-x)
int dis[maxn],len;
struct point{
	int d;
	double c;
	void clear(){d=0,c=0.0;}
	void operator += (const point &x){
		if(x.d>d)*this=x;
		else if(x.d==d)c+=x.c;
	}
	void operator << (const point &x){
		if(x.d+1>d)*this=x,++d;
		else if(x.d+1==d)c+=x.c;
	}
	point(){d=1,c=1.0;}
}f[maxn],g[maxn],dat[maxn];
struct miss{
	int x,y;
	point *ans;
}a[maxn],b[maxn],base[maxn];
inline bool cmp1(miss &a,miss &b){
	return a.x>b.x;
}
inline bool cmp2(miss &a,miss &b){
	return a.x<b.x;
}
inline void add(int x,point &d){
	while(x<=len)dat[x]+=d,x+=lowb(x);
}
inline void clear(int x){
	while(x<=len)dat[x].clear(),x+=lowb(x);
}
inline point ask(int x){
	point ans;
	ans.clear();
	while(x)ans+=dat[x],x-=lowb(x);
	return ans;
}
void cdq1(int l,int r){
	if(l==r)return;
	int mid=l+r>>1,pl=l,pr=mid+1;
	cdq1(l,mid);
	for(register int i=mid+1;i<=r;++i)b[i]=a[i];
	sort(b+mid+1,b+r+1,cmp1);
	while(pl<=mid&&pr<=r){
		while(pl<=mid&&a[pl].x>=b[pr].x)add(len-a[pl].y+1,*a[pl].ans),++pl;
		*b[pr].ans<<ask(len-b[pr].y+1),++pr;
	}
	while(pr<=r)*b[pr].ans<<ask(len-b[pr].y+1),++pr;
	for(register int i=l;i<pl;++i)clear(len-a[i].y+1);
	cdq1(mid+1,r);
	sort(a+l,a+r+1,cmp1);
}
void cdq2(int l,int r){
	if(l==r)return;
	int mid=l+r>>1,pl=l,pr=mid+1;
	cdq2(l,mid);
	for(register int i=mid+1;i<=r;++i)b[i]=a[i];
	sort(b+mid+1,b+r+1,cmp2);
	while(pl<=mid&&pr<=r){
		while(pl<=mid&&a[pl].x<=b[pr].x)add(a[pl].y,*a[pl].ans),++pl;
		*b[pr].ans<<ask(b[pr].y),++pr;
	}
	while(pr<=r)*b[pr].ans<<ask(b[pr].y),++pr;
	for(register int i=l;i<pl;++i)clear(a[i].y);
	cdq2(mid+1,r);
	sort(a+l,a+r+1,cmp2);
}
int main(){
	int n=read();
	point ans;
	ans.clear();
	for(register int i=1;i<=n;++i)base[i].x=read(),base[i].y=dis[i]=read();
	sort(dis+1,dis+1+n);
	len=unique(dis+1,dis+1+n)-dis-1;
	for(register int i=1;i<=n;++i)base[i].y=lower_bound(dis+1,dis+1+len,base[i].y)-dis,a[i]=base[i],a[i].ans=f+i;
	for(register int i=1;i<=len;++i)dat[i].clear();
	cdq1(1,n);
	for(register int i=1,j=n;i<j;++i,--j)swap(base[i],base[j]);
	for(register int i=1;i<=n;++i)a[i]=base[i],a[i].ans=g+n-i+1;
	cdq2(1,n);
	for(register int i=1;i<=n;++i)ans+=f[i];
	printf("%d\n",ans.d);
	for(register int i=1;i<=n;++i)
		if(f[i].d+g[i].d-1==ans.d)printf("%.5lf ",f[i].c*g[i].c/ans.c);
		else printf("0.00000 ");
}
```

其实$cdq$分治还能维护斜率优化然而我不会斜率优化告辞。

---

# 水题

## 穷人的眼泪

三道纯三维偏序板子，然而都是$bzoj$权限题

[3963](https://www.lydsy.com/JudgeOnline/problem.php?id=3963)

[2253](https://www.lydsy.com/JudgeOnline/problem.php?id=2253)

[4430](https://www.lydsy.com/JudgeOnline/problem.php?id=4430)

## [天使玩偶/SJY摆棋子](https://www.luogu.org/problem/P4169)

去掉绝对值，分四种情况讨论：在询问点左上、左下、右上、右下的点。

然后就成了三维分别为时间、$x$、$y$的最小值问题。跑四遍$cdq$，树状数组维护前/后缀最小值，复杂度$O((n+m)\log^2n)$。

可能会卡常。每次$cdq$分治前预处理已有的$n$个点，按$x$为第一关键字，$y$为第二关键字排序，分治区间为在$[1,n]$时直接返回，复杂度降为$O(m\log^2 n)$。

## [动态逆序对](https://www.luogu.org/problem/P3157)

每个元素有被删除时间$t$、位置和权值$a_i$。

一个元素被删除后减少的逆序对为满足$t_j>t_i,j< i,a_j>a_i$和$t_j>t_i,j>i,a_j<a_i$的个数。

跑两遍$cdq$分治即可。

## [Mokia 摩基亚](https://www.luogu.org/problem/P4390)

动态二维数点。

把查询矩形差分成四个矩形，然后就是时间、横坐标、纵坐标的三维偏序了。

## [序列](https://www.luogu.org/problem/P4093)

以前拿树套树写的。

记$mi(i)$为位置$i$出现过的最小值，$ma(i)$为位置$i$出现过的最大值。

还是做一个$LIS$：

$f(i)=\max\limits_{j=1}^{i-1}\{f(j)+1\}(mi(i)\ge a_j,a_i\ge ma(j))$

$cdq$分治优化即可。

---

真的找不到$cdq$分治的题啊$QAQ$