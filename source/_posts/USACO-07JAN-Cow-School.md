---
title: USACO-07JAN Cow School
date: 2019-12-30 17:16:45
categories: 题解
tags:
    - 数据结构
    - cdq分治
    - K-D Tree
    - 单调队列
keywords: Cow School
description:
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.9.2/cowschool.jpg
mathjax: true
---

[传送门](https://www.luogu.com.cn/problem/P2877)

试图用$kdt$强上凸包の翻车现场。

<!--more-->

以下记$a_i$为$t[i]$，$b_i$为$p[i]$，$S$为去掉得分比前$d$小后的$\sum a_i$，$T$为去掉前$d$小后的$\sum b_i$。

如果存在一种更优的方案，一定可以在前$d$小里选$1$场替换掉已有的$1$场，使得分更大。

即$\dfrac{S-a_i+a_j}{T-b_i+b_j}>\dfrac{S}{T}(j\le d <i)$

> update：
> 关于这个结论的证明我出胡策的时候发现不是很显然，所以还是简单证一下。
> 
> 最大的问题在于：如果存在某种方案用集合$A$替换集合$B$是较优的，而对于$i\in A,j\in B$，仅用$i$替换$j$并不优，这个结论是否就不成立了呢。
> 
> 感性理解，如果对于所有$i\in A,j\in B$满足仅用$i$替换$j$并不优，好像这个$A$换$B$也不太可能成为较优解了。
> 
> ~~啰嗦化~~形式化证明的话，这个条件可以这么表达：
> 
> $\exists i,j\begin{cases}\dfrac{S-a_i+a_j}{T-b_i+b_j}<\dfrac{S}{T}\\\dfrac{S-a_i-C+a_j+C'}{T-b_i-D+b_j+D'}>\dfrac{S}{T}\end{cases}$
> 
> 其中，$C=\left(\sum\limits_{k\in B}a_k\right)-a_i,C'=\left(\sum\limits_{k\in A}a_k\right)-a_j,D=\left(\sum\limits_{k\in B}b_k\right)-b_i,D'=\left(\sum\limits_{k\in A}b_k\right)-b_j$
> 
> 化一下式子就能得到$\dfrac{S-C+C'}{T-D+D'}>\dfrac{S}{T}$
> 
> 也就是说从$A,B$去掉$i,j$这对不优解，剩下的元素依然可以成为一个较优解。那么如果$A,B$中所有$i,j$都不优的话，就去掉一组得到新集合。直到存在$i,j$较优或者都只剩$1$个元素，结论就成立了。

化简一波这个式子就成了$Ta_j-Sb_j>Ta_i-Sb_i$

于是选出最大的$Ta_j-Sb_j$和最小的$Ta_i-Sb_i$判断一下即可。

怎么搞呢？

---

第一想法：我们发现这个式子长得很$kdt$（参见[巧克力王国](https://www.luogu.com.cn/problem/P4475)）。

直接上$kdt$瞎搞一搞就行了，计算矩形端点的取值剪枝。**复杂度没有保证，反正$kdt$是暴力**。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <time.h>

#define maxn 50005

const long long inf = 1e18;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
#define son(x,y) son[x][y]
#define ls(x) son(x,0)
#define rs(x) son(x,1)
#define up(f,a,b) a[node][c[node]]=f(f(a[ls(node)][c[node]],a[rs(node)][c[node]]),p[node][b])
#define dup(f,a) a[node][c[node]^1]=f(a[ls(node)][c[node]^1],a[rs(node)][c[node]^1])
long long s,t,res;
int ans[maxn],id[maxn],fa[maxn],son[maxn][2],xl[maxn][2],xr[maxn][2],yl[maxn][2],yr[maxn][2],p[maxn][2],root,cnt,D;
bool c[maxn];
struct grade{
	int a,b;
	bool operator < (const grade &x)const{return a*x.b<b*x.a;}
}g[maxn];
struct point{
	int pos[2],id;
	bool operator < (const point &x)const{return pos[D]<x.pos[D];}
}po[maxn];
inline void update(int node){
	up(min,xl,0),up(max,xr,0),up(min,yl,1),up(max,yr,1);
	dup(min,xl),dup(max,xr),dup(min,yl),dup(max,yr);
}
void build(int l,int r,int &node){
	if(l>r)return;
	int mid=l+r>>1;
	node=++cnt,D=rand()&1;
	nth_element(po+l,po+mid,po+r+1);
	memcpy(p[node],po[mid].pos,sizeof p[node]),id[po[mid].id]=node;
	build(l,mid-1,ls(node)),build(mid+1,r,rs(node));
	fa[ls(node)]=fa[rs(node)]=node;
	update(node);
}
inline void toggle(int node){
	c[node]=1;
	while(node)update(node),node=fa[node];
}
inline long long calc_max(int node){return node?t*xr[node][1]-s*yl[node][1]:-inf;}
inline long long calc_min(int node){return node?t*xl[node][0]-s*yr[node][0]:inf;}
void Max(int node){
	if(c[node])res=max(res,t*p[node][0]-s*p[node][1]);
	long long d[2]={calc_max(ls(node)),calc_max(rs(node))};
	int t=d[1]>d[0];
	if(d[t]>res)Max(son(node,t));
	if(d[t^1]>res)Max(son(node,t^1));
}
void Min(int node){
	if(!c[node])res=min(res,t*p[node][0]-s*p[node][1]);
	long long d[2]={calc_min(ls(node)),calc_min(rs(node))};
	int t=d[1]<d[0];
	if(d[t]<res)Min(son(node,t));
	if(d[t^1]<res)Min(son(node,t^1));
}
inline long long ma(){res=-inf,Max(root);return res;}
inline long long mi(){res=inf,Min(root);return res;}
int main(){
	srand(time(0));
	int n=read(),cnt=0;
	for(register int i=1;i<=n;++i)s+=(g[i].a=read()),t+=(g[i].b=read());
	sort(g+1,g+1+n);
	for(register int i=1;i<=n;++i)po[i].id=i,po[i].pos[0]=g[i].a,po[i].pos[1]=g[i].b;
	build(1,n,root);
	for(register int i=1;i<n;++i){
		s-=g[i].a,t-=g[i].b,toggle(id[i]);
		if(ma()>mi())ans[++cnt]=i;
	}
	printf("%d\n",cnt);
	for(register int i=1;i<=cnt;++i)printf("%d\n",ans[i]);
}
```

$kdt$丰硕的战果：

- **复杂度没有保证**：最后一个点跑了$29s$
- **反正$kdt$是暴力**：喜提和暴力一个分

<img src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.0/emojis/else/16.gif">

---

重新考察这个式子$Ta_j-Sb_j>Ta_i-Sb_i$：

令$Ta_i-Sb_i=C$，变形一波又成了$\frac{T}{S}a_i-\frac{C}{S}=b_i$。

把$\frac{T}{S}$看作斜率，$\frac{C}{S}$为截距。目的是最小/最大化$C$。

问题就很斜率优化了。维护若干个点，支持：

- 插入一个点
- 给定一条直线的斜率，使它过一个点，最大/小化截距

显然斜率是单调的。没什么高论，两遍$cdq$分治+单调队列维护凸包解决。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 50005

const long long inf = 1e18;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
inline double calc(int x1,int y1,int x2,int y2){if(x1==x2)return 1e18;return double(y2-y1)/(x2-x1);}
int ans[maxn];
long long mi[maxn],ma[maxn];
struct grade{
	int a,b;
	bool operator < (const grade &x)const{return a*x.b<b*x.a;}
}g[maxn];
struct operation{
	bool t;//0 insert  1 ask
	int id;
	long long x,y;//x:T   y:S
}A[maxn<<1],B[maxn<<1];
struct Monoqueue{
	int xx[maxn],yy[maxn],pos[maxn],head,tail;
	void clear(){head=1,tail=0;}
	void push1(int x,int y,int p){
		while(head<tail&&calc(xx[tail],yy[tail],xx[tail-1],yy[tail-1])>calc(xx[tail],yy[tail],x,y))--tail;
		xx[++tail]=x,yy[tail]=y,pos[tail]=p;
	}
	int front1(double k){
		while(head<tail&&calc(xx[head],yy[head],xx[head+1],yy[head+1])<k)++head;
		if(head<=tail)return pos[head];
		return -1;
	}
	void push2(int x,int y,int p){
		while(head<tail&&calc(xx[tail],yy[tail],xx[tail-1],yy[tail-1])<calc(xx[tail],yy[tail],x,y))--tail;
		xx[++tail]=x,yy[tail]=y,pos[tail]=p;
	}
	int front2(double k){
		while(head<tail&&calc(xx[head],yy[head],xx[head+1],yy[head+1])>k)++head;
		return pos[head];
	}
	Monoqueue(){clear();}
}q;
void cdq1(int l,int r){
	if(l==r)return;
	int mid=l+r>>1,pl=l,pr=mid+1,p=l;
	cdq1(l,mid),q.clear();
	for(register int i=l;i<=mid;++i)if(!A[i].t)q.push1(A[i].x,A[i].y,A[i].id);
	for(register int i=mid+1;i<=r;++i){
		if(!A[i].t)continue;
		int j=q.front1((double)A[i].x/A[i].y);
		if(~j)mi[A[i].id]=min(mi[A[i].id],A[i].x*g[j].a-A[i].y*g[j].b);
	}
	cdq1(mid+1,r),q.clear();
	while(pl<=mid&&pr<=r){
		if(A[pl].x>A[pr].x||(A[pl].x==A[pr].y&&A[pl].y<A[pr].y))B[p++]=A[pl++];
		else B[p++]=A[pr++];
	}
	while(pl<=mid)B[p++]=A[pl++];
	while(pr<=r)B[p++]=A[pr++];
	for(register int i=l;i<=r;++i)A[i]=B[i];
}
void cdq2(int l,int r){
	if(l==r)return;
	int mid=l+r>>1,pl=l,pr=mid+1,p=l;
	cdq2(l,mid),q.clear();
	for(register int i=l;i<=mid;++i)if(!A[i].t)q.push2(A[i].x,A[i].y,A[i].id);
	for(register int i=mid+1;i<=r;++i){
		if(!A[i].t)continue;
		int j=q.front2((double)A[i].x/A[i].y);
		if(~j)ma[A[i].id]=max(ma[A[i].id],A[i].x*g[j].a-A[i].y*g[j].b);
	}
	cdq2(mid+1,r),q.clear();
	while(pl<=mid&&pr<=r){
		if(A[pl].x>A[pr].x||(A[pl].x==A[pr].x&&A[pl].y>A[pr].y))B[p++]=A[pl++];
		else B[p++]=A[pr++];
	}
	while(pl<=mid)B[p++]=A[pl++];
	while(pr<=r)B[p++]=A[pr++];
	for(register int i=l;i<=r;++i)A[i]=B[i];
}
int main(){
	memset(mi,0x3f,sizeof mi),memset(ma,~0x3f,sizeof ma);
	long long s=0,t=0;
	int n=read(),cnt=0,len=0;
	for(register int i=1;i<=n;++i)g[i].a=read(),g[i].b=read();
	sort(g+1,g+1+n);
	for(register int i=n;i;--i){
		A[++len]=(operation){1,i,t,s};
		A[++len]=(operation){0,i,g[i].a,g[i].b};
		s+=g[i].a,t+=g[i].b;
	}
	cdq1(1,len);
	len=0;
	for(register int i=1;i<=n;++i){
		s-=g[i].a,t-=g[i].b;
		A[++len]=(operation){0,i,g[i].a,g[i].b};
		A[++len]=(operation){1,i,t,s};
	}
	cdq2(1,len);
	for(register int i=1;i<n;++i)if(ma[i]>mi[i])ans[++cnt]=i;
	printf("%d\n",cnt);
	for(register int i=1;i<=cnt;++i)printf("%d\n",ans[i]);
}
```