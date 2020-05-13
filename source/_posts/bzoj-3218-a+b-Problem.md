---
title: bzoj 3218 a+b Problem
date: 2019-04-28 21:37:34
categories: 题解
tags:
	- 图论
	- 网络流
	- 最小割
	- 主席树
description: 在比赛上见到这个题，发现我根本就不会网络流，连经典模型都不会。嘤嘤嘤。
keywords: a+b Problem
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/a+bproblem.jpg
mathjax: true
---

[通往bzojの传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=3218)

[通往51nodの传送门](http://www.51nod.com/Contest/Problem.html#!#contestProblemId=845)

在比赛上见到这个题，发现我根本就不会网络流，连经典模型都不会。嘤嘤嘤

<span class="spoiler">不过厚颜无耻地在zhuoer和题解的帮助下A掉了</span>

<!--more-->

首先进行小学生都会的移项变换：

$\ \sum\limits_{i\in white} w_i+\sum\limits_{i\in black}b_i-\sum\limits_{i\in bad}p_i\\=\sum(w_i+b_i)-\sum\limits_{i\in black}w_i-\sum\limits_{i\in white}b_i-\sum\limits_{i\in bad}p_i$

建模：

每个点$i$分出来一个$i'$。

- 源点向$i$连流量为$b_i$的边
- $i$向汇点连流量为$w_i$的边
- $i$向$i'$连流量为$p_i$的边
- $i'$向所有能使$i$变坏的点$j$连流量为$inf$的边

就是酱紫：

![](\images\a+b problem-1.png)

答案就是$\sum(w_i+b_i)$减去最小割。

对于点$i$，为了使源汇不连通，首先要么割掉$b_i$，要么割掉$w_i$。前者代表它为白点，后者代表它为黑点。

如果割$b_i$，已经满足不联通了，就不用考虑$p_i$。

如果割$w_i$，若$j$割了$b_j$（即$j$为白点），则还有一条路径$S\rightarrow i\rightarrow i'\rightarrow j\rightarrow T$。

在$p_i$、$b_i$、$w_j$里割一条边。割掉它们有啥意义呢？

- 若割掉$p_i$，就是付出坏点的代价
- 若割掉$b_i$，把$i$点由黑点改为白点
- 若割掉$w_j$，把$j$点由白点改为黑点

为啥要开一个$i'$连$inf$边呢？

$i$可能有好几个$j$要连，但是多个满足条件的$j$只有会产生一次$p_i$，所以拆点限制。

> Q：为啥这种沙雕题要写这么详细？
>
> A：因为我太蒻了，看了题解搞了很久才明白。。。

边数是$n^2$的，主席树优化建图达到$n\log n$

代码：

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
#define ls(x) ls[x]
#define rs(x) rs[x]
int line[maxn<<5],st,en,h[maxn<<5],fl[maxn<<5],ls[maxn<<5],rs[maxn<<5],id[maxn],root[maxn],head,tail,num=1,cnt;
int a[maxn],b[maxn],ll[maxn],rr[maxn],p[maxn],w[maxn],dis[maxn<<2],len;
struct edge{
	int pre,to,flow;
}e[maxn<<6];
inline void add(int from,int to,int l){
	if(!from||!to)return;
	e[++num].pre=h[from],h[from]=num,e[num].to=to,e[num].flow=l;
	e[++num].pre=h[to],h[to]=num,e[num].to=from,e[num].flow=0;
}
bool bfs(){
	memset(fl,0,sizeof fl);
	fl[st]=tail=1,head=0;
	while(head<tail){
		int node=line[++head],x;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			if(!fl[x]&&e[i].flow){
				fl[x]=fl[node]+1;
				if(x==en)return 1;
				line[++tail]=x;
			}
		}
	}	
	return 0;
}
int dfs(int node=st,int qu=inf){
	if(node==en)return qu;
	int rest=qu,x,k;
	for(register int i=h[node];i&&rest;i=e[i].pre){
		x=e[i].to;
		if(fl[x]==fl[node]+1&&e[i].flow){
			k=dfs(x,min(e[i].flow,rest));
			if(!k)fl[x]=0;
			else rest-=k,e[i].flow-=k,e[i^1].flow+=k;
		}
	}
	return qu-rest;
}
void build(int poi,int l,int r,int &node,int ol){
	node=++cnt,add(node,ol,inf);
	if(l==r){
		add(node,poi,inf);
		return;
	}
	int mid=l+r>>1,ans;
	if(a[poi]<=mid)rs(node)=rs(ol),build(poi,l,mid,ls(node),ls(ol));
	else ls(node)=ls(ol),build(poi,mid+1,r,rs(node),rs(ol));
	add(node,ls(node),inf),add(node,rs(node),inf);
}
void modify(int L,int R,int l,int r,int node,int poi){
	if(!node)return;
	if(L<=l&&R>=r){add(poi,node,inf);return;}
	int mid=l+r>>1;
	if(L<=mid)modify(L,R,l,mid,ls(node),poi);
	if(R>mid)modify(L,R,mid+1,r,rs(node),poi);
}
int main(){
	int n=read(),ans=0;
	cnt=(n<<1)+2;
	for(register int i=1;i<=n;++i)
		dis[++len]=a[i]=read(),b[i]=read(),w[i]=read(),dis[++len]=ll[i]=read(),dis[++len]=rr[i]=read(),p[i]=read();
	sort(dis+1,dis+1+len);
	len=unique(dis+1,dis+1+len)-dis-1;
	for(register int i=1;i<=n;++i){
		a[i]=lower_bound(dis+1,dis+1+len,a[i])-dis;
		ll[i]=lower_bound(dis+1,dis+1+len,ll[i])-dis;
		rr[i]=lower_bound(dis+1,dis+1+len,rr[i])-dis;
		build(i,1,len,root[i],root[i-1]);
	}
	line[1]=st=cnt+n+n+1,en=st+1;
	for(register int i=1;i<=n;++i){
		add(st,i,b[i]),add(i,i+n,p[i]),add(i,en,w[i]);
		modify(ll[i],rr[i],1,len,root[i-1],i+n),ans+=b[i]+w[i];	
	}
	while(bfs())ans-=dfs();
	printf("%d\n",ans);
}

```

