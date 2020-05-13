---
title: Codeforces1215F Radio Stations
date: 2019-12-27 14:32:45
categories: 题解
tags: 
    - 图论
    - tarjan
    - 2-SAT
keywords: Radio Stations
description: 合 格 考 前 一 天 刚 把 政 治 书 上 的 光 盘 撕 下 来 的 屑
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.8.7/radiostations.jpg
mathjax: true
---

[传送门](https://www.luogu.com.cn/problem/CF1215F)

合 格 考 前 一 天 刚 把 政 治 书 上 的 光 盘 撕 下 来 的 屑<img class="emoji_coda" src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.8.8/emojis/chino/3.png" style="height:70px;width:auto">

<!--more-->

题意：

> 有$n$个电台，每个电台有一个频段$[l_i,r_i]$。有主频$f$，可以在取$[1,v]$中任意整数。现在要~~大清洗~~保留一些电台。如果$f\in [l_i,r_i]$，则可以保留电台$i$，否则不能。有$m_1$个限制形如$x_i$和$y_i$，表示$x_i$和$y_i$至少保留一个；有$m_2$个限制形如$u_i$和$v_i$，表示$u_i$和$v_i$最多保留一个。要求你钦定$f$和保留的电台，满足这些限制。不存在合法方案输出`-1`，否则输出方案。

这$m_1+m_2$个限制一脸$2-SAT$了，按题意连上就好了。

关键是$f$的限制。一个很$NAIVE$的想法是把$f$拆成$v$个点，表示$f$的取值，大概还要套个线段树优化建图。发现这样不大行。

还是把$f$拆成$v$个点，记为$le(i)$，表示$f\le i$。

这样就可以连边$i\rightarrow le(r_i),le(l_i-1)\rightarrow i'$。

这限制还不太够，再来$v$个点，记为$ge(i)$，表示$f>i$。

于是又可以连边$i\rightarrow ge(l_i-1),ge(r_i)\rightarrow i'$。

$le$和$ge$内部也有些关系：$le(i)\rightarrow le(i+1),ge(i)\rightarrow ge(i-1)$。

跑一遍缩点，判断$i$和$i'$是否在同一个$SCC$里即可。为什么不用判$le(i)$和$ge(i)$？手玩可以发现，当$le(i)$和$ge(i)$在同一$SCC$时，必有一对$i$和$i'$在同一$SCC$里，也就不用管了。

输出方案按$2-SAT$板子一样，$f$为选取的电台中$l_i$的最大值。不用考虑$r_i$，因为如果不合法了，判$SCC$就过不去。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 400005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
#define le(x) ((n<<1)+x)
#define ge(x) ((n<<1)+v+1+x)
int h[maxn<<2],seg[maxn<<2],low[maxn<<2],sta[maxn<<2],id[maxn<<2],ans[maxn],l[maxn],r[maxn],top,cnt,num,all;
bool vis[maxn<<2];
struct edge{int pre,to;}e[maxn*10];
inline void add(int from,int to){e[++num]=(edge){h[from],to},h[from]=num;}
void TJ(int node){
	vis[sta[++top]=node]=1;
	seg[node]=low[node]=++cnt;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(!seg[x])TJ(x),low[node]=min(low[node],low[x]);
		else if(vis[x])low[node]=min(low[node],seg[x]);
	}
	if(seg[node]==low[node]){
		id[node]=++all,vis[node]=0;
		while(sta[top]!=node)id[sta[top]]=all,vis[sta[top--]]=0;
		--top;
	}
}
int main(){
	int m=read(),n=read(),v=read(),M=read(),f=0;
	while(m--){
		int x=read(),y=read();
		add(x+n,y),add(y+n,x);
	}
	for(register int i=v;i>1;--i)add(le(i-1),le(i));
	for(register int i=0;i<v-1;++i)add(ge(i+1),ge(i));
	for(register int i=1;i<=n;++i){
		l[i]=read(),r[i]=read();
		add(i,le(r[i]));
		add(i,ge(l[i]-1));
		if(r[i]<v)add(ge(r[i]),i+n);
		if(l[i]>1)add(le(l[i]-1),i+n);
	}
	while(M--){
		int x=read(),y=read();
		add(x,y+n),add(y,x+n);
	}
	for(register int i=1;i<=(n<<1)+(v<<1);++i)if(!seg[i])TJ(i);
	cnt=0;
	for(register int i=1;i<=n;++i){
		if(id[i]==id[i+n]){puts("-1");return 0;}
		if(id[i]<id[i+n])f=max(f,l[i]),ans[++cnt]=i;
	}
	printf("%d %d\n",cnt,f);
	for(register int i=1;i<=cnt;++i)printf("%d ",ans[i]);
	pn;
}
```