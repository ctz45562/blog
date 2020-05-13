---
title: Codeforces1153D Serval and Rooted Tree
date: 2019-10-22 20:09:28
categories: 题解
tags:
	- 动态规划
keywords: Serval and Rooted Tree
description: 朝奈池最后一个晚上，抽了100多发的我已经看淡了一切
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/servalandrootedtree.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF1153D)

朝奈池最后一个晚上，抽了100多发的我已经看淡了一切{% emoji_coda luotianyi/冷漠 %}

<!--more-->

这$DP$有点神仙啊。

设$f(i)$为点$i$的权值在其子树内的叶子中，能取到的最靠前的排名。

- 若$i$为叶子，$f(i)=1$
- 若$i$操作为$\max$，取所有儿子中排名最靠前的，$f(i)=\min\limits_{j\in son(i)}\{f(j)\}$
- 若$i$操作为$\min$，$i$需要给所有儿子的$f$分配名额，$f(i)=\sum\limits_{j\in son(i)}f(j)$

答案就是叶子数$-f(1)+1$

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 300005
#define inf 0x3f3f3f3f
#define px putchar
#define pn px('\n')
#define ps px(' ')
#define pd puts("======================")
#define pj puts("++++++++++++++++++++++")

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int f[maxn],h[maxn],num;
bool leaf[maxn],type[maxn];
struct edge{int pre,to;}e[maxn];
inline void add(int from,int to){leaf[from]=1,e[++num]=(edge){h[from],to},h[from]=num;}
void dp(int node=1){
	if(!leaf[node]){f[node]=1;return;}
	f[node]=type[node]?inf:0;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to,dp(x);
		f[node]=type[node]?min(f[node],f[x]):f[node]+f[x];
	}
}
int main(){
	int n=read(),ans=0;
	for(register int i=1;i<=n;++i)type[i]=read();
	for(register int i=2;i<=n;++i)add(read(),i);
	dp();
	for(register int i=1;i<=n;++i)ans+=leaf[i]^1;
	printf("%d\n",ans-f[1]+1);
}
```

