---
title: loliの胡策 Walk
date: 2019-09-08 11:15:25
categories: 题解
tags:
	- 动态规划
	- 枚举
keywords: Walk
description: 教练某场胡策的T2。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/lolidhcwalk.jpg
mathjax: true
---

教练某场胡策的$T2$。

<!--more-->

# 题目

## 描述

> 给定一棵$n$个节点的树，每条边的长度为$1$，同时有一个权值
> $w$。定义一条路径的权值为路径上所有边的权值的最大公约数。现在
> 对于任意$i\in[1,n]$，求树上所有长度为$i$的简单路径中权值最大的
> 是多少。如果不存在长度为$i$的路径，则第$i$行输出$0$。

## 输入输出格式

### 输入格式

第一行，一个整数$n$，表示树的大小。 
 接下来$n-1$行，每行三个整数$u,v,w$，表示$u,v$间存在一条权值
为$w$的边。 

### 输出格式

对于每种长度，输出一行，表示答案。 

## 样例

### 输入样例

3 
1 2 3 
1 3 9

### 输出样例

9 
3 
0 

## 数据范围

对于$30\%$的数据，$n\le 1000$。 
对于额外$30\%$的数据，$w\le 100$。 
对于$100\%$的数据，$n\le4*10^5$，$1\le u,v\le n$，$w\le 10^6$。 

# 题解

考虑$w\le 100$的数据。

暴力枚举$gcd$，忽略树上$w\nmid gcd$的边。显然答案有单调性，将树的直径的答案更新为当前$gcd$。最后倒着取$\max$即可。

复杂度$O(nw)$。

正解：

还是枚举$gcd$，把$w$为$gcd$的倍数的边建出一个森林，求森林的直径更新即可。

注意到只有当枚举到$w$的约数时，该边才会被加进去。所以复杂度为所有边的约数个数和。

而$10^6$内最大的约数个数只有$128$。

复杂度为跑不满的$O(128n+w\log w)$。$w\log w$是枚举倍数的调和级数。

# 代码

``` cpp
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
int f[maxn],ans[maxn],h[maxn],num,now,len;
vector<pair<int,int> >ed[1000005];
vector<int>poi;
bool vis[maxn],app[maxn];
struct edge{
	int pre,to;
}e[maxn<<1];
inline void add(int from,int to){
	e[++num].pre=h[from],h[from]=num,e[num].to=to;
}
void dp(int node){
	f[node]=0,vis[node]=1;
	int x;
	for(register int i=h[node];i;i=e[i].pre){
		x=e[i].to;
		if(vis[x])continue;
		dp(x);
		len=max(len,f[node]+f[x]+1);
		f[node]=max(f[node],f[x]+1);
	}
}
int main(){
	int n=read(),x,y,z,tp=0;
	for(register int i=1;i<n;++i)x=read(),y=read(),tp=max(z=read(),tp),ed[z].push_back((pair<int,int>){x,y});
	for(register int i=1;i<=tp;++i){
		poi.clear(),num=len=0;
		for(register int j=i;j<=tp;j+=i){
			for(vector<pair<int,int> >::iterator iter=ed[j].begin();iter!=ed[j].end();++iter){
				if(!app[iter->first])poi.push_back(iter->first),app[iter->first]=1,h[iter->first]=0;
				if(!app[iter->second])poi.push_back(iter->second),app[iter->second]=1,h[iter->second]=0;
				add(iter->first,iter->second),add(iter->second,iter->first);
			}
		}
		for(register int j=0;j<poi.size();++j)
			if(!vis[poi[j]])dp(poi[j]);
		for(register int j=0;j<poi.size();++j)app[poi[j]]=vis[poi[j]]=0;
		ans[len]=i;
	}
	for(register int i=n-1;i;--i)ans[i]=max(ans[i],ans[i+1]);
	for(register int i=1;i<=n;++i)
		printf("%d\n",ans[i]);
}
```

