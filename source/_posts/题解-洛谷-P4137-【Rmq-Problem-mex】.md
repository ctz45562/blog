---
title: 洛谷 P4137 Rmq Problem / mex
date: 2019-03-15 07:51:15
categories: 题解
tags:
	- 数据结构
	- 线段树
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/rmqproblemmex.jpg
keywords: Rmq Problem / mex
description: 一道水题想了半天。。。
---

[传送门](https://www.luogu.org/problemnew/show/P4137)

一道水题想了半天。。。

<!--more-->

可以想到造棵权值线段树，查询时如果左儿子有这个区间内没有出现的数就走左儿子，否则走右儿子。如果用普通的主席树维护每个数出现的次数，就会发现这玩意不具有区间可减性，不是基于前缀和的主席树能维护的。

换个思路：对于第$i$棵权值线段树，维护一下**数列前$i$项中每个权值出现的最靠右的位置**。然后向上更新一下最小值。这样如果某个权值最靠右的位置都比查询区间左端点小的话，那它一定没有在这个区间里出现。维护最小值目的就是看最小的是否比左端点大，根据这个在线段树上二分。

权值范围是$10^9$的。题解里有说答案最大为$n$，直接把$>n$的$a[i]$处理为$n+1$就行。蒟蒻没有想到$QAQ$，说一下蒟蒻的处理：

用离散化，但是一个权值没有在数列里出现过的话就不会在离散化数组中，也不会出现在线段树中，查询会出锅。注意到一个询问的答案要么是$0$，要么是数列中某个数的值$+1$。这样离散化时把$0$和每个出现过的权值$+1$都丢进去就行了$QwQ$。

当然这道题不强制在线也不修改，可以把询问离线下来一边扫一边加，遇到询问右端点就查询左端点，不需要主席树（可持久化线段树），一棵权值线段树就可以了。

时间复杂度：$O(nlogn)$

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

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
template<typename T>
inline T read(){
	T x=0;
	int y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int dis[maxn],ll[maxn],ans[maxn],len,a[maxn],n,m;
//dis是离散化数组，ll是每个询问的左端点，ans是答案
vector<int>line[maxn];
//line用来离线存每个询问
struct Segment_Tree{
#define ls(x) (x<<1)
#define rs(x) (x<<1|1)
	int dat[maxn<<2];
	inline void update(int node){
		dat[node]=min(dat[ls(node)],dat[rs(node)]);
	}
	void add(int poi,int l,int r,int node,int d){
		if(l==r){
			dat[node]=d;
            //因为是离线边扫边加，当前更新的位置一定是最靠右的，所以直接覆盖掉就行
			return;
		}
		int mid=l+r>>1;
		if(poi<=mid)add(poi,l,mid,ls(node),d);
		else add(poi,mid+1,r,rs(node),d);
		update(node);
	}
	int ask(int d){
		int l=1,r=len,node=1;
		while(l<r){
			int mid=l+r>>1;
			if(dat[ls(node)]<d)r=mid,node=ls(node);
            //左区间有最靠右的位置都比左端点小的，一定没出现过，向左走
			else l=mid+1,node=rs(node);
            //否则向右走
		}
		return dis[l];
	}
}st;
int main(){
	n=read(),m=read();
	for(register int i=1;i<=n;++i)
		dis[++len]=a[i]=read(),dis[++len]=a[i]+1;
    //把每个值+1添进去
	dis[++len]=0;
    //把0添进去
	sort(dis+1,dis+1+len);
	len=unique(dis+1,dis+1+len)-dis-1;
	for(register int i=1;i<=m;++i){
		ll[i]=read();
		line[read()].push_back(i);
	}
	for(register int i=1;i<=n;++i){
		a[i]=lower_bound(dis+1,dis+1+len,a[i])-dis;
		st.add(a[i],1,len,1,i);
		for(int j=0;j<line[i].size();++j){
			int k=line[i][j];
			ans[k]=st.ask(ll[k]);
		}
	}
	for(register int i=1;i<=m;++i)
		printf("%d\n",ans[i]);
}

```

