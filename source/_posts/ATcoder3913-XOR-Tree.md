---
title: ATcoder3913 XOR Tree
date: 2019-10-28 11:47:49
categories: 题解
tags:
	- 动态规划
	- 贪心
	- 记忆化搜索
	- 状态压缩
keywords: XOR Tree
description: 青岛集训是你，DP题单是你，今天比赛还是你。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/xortree.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/AT3913)

青岛集训是你，$DP$题单是你，今天比赛还是你。

大概这就是缘分吧。<span class="spoiler">你不要过来啊！</span>

<!--more-->

定义一个点的点权为与它相连的边的异或和。

结论是点权全为$0$等价于边权全为$0$。~~证明的话，使用逆反证法，因为举不出反例所以是对的。~~

对一条路径异或$x$，路径端点其中一条边被异或，点权异或$x$，而中间的点因为有两条边被异或，抵消了。这个操作就转成了任取两个点使它们异或上同一个值。

首先有一个显然的贪心：每次取两个相同的点权异或它们自己，一次操作能消掉两个数。

之后每种点权最多剩下$1$个。而且点权最大不超过$15$，这玩意可以状压。

设$f(S)$为状态为$S$（$0/1$表示每种点权的数量）全部消成$0$的最小次数。

每次操作我们可以枚举两个数量为$1$的数$x,y$异或上$x$，$x,y$就没了，产生了一个$x\ xor\ y$（记为$z$）。

讨论一下：

- $z\notin S$，直接把$z$加进去，$f(S)=f(S\ xor\ (1<<x)\ xor\ (1<<y)\ xor\ (1<<z))+1$
- $z\in S$，这时会出现两个$z$，根据刚才的贪心再来一步消掉两个$z$是最优的，$f(S)=f(S\ xor\ (1<<x)\ xor\ (1<<y)\ xor\ (1<<z))+2$

这个$DP$转移方向很鬼畜，不是很好递推，直接记搜就行。

复杂度$O(n+w^22^w)$

代码：

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
int a[maxn],tax[16],f[1<<16];
int dfs(int s){
	if(~f[s])return f[s];
	int x;
	f[s]=inf;
	for(register int i=1;i<=15;++i)
		for(register int j=1;j<=15;++j){
			if(i==j||!(s>>i-1&1)||!(s>>j-1&1))continue;
			x=i^j;
			if(s>>x-1&1)f[s]=min(f[s],dfs(s^(1<<i-1)^(1<<j-1)^(1<<x-1))+2);
			else f[s]=min(f[s],dfs(s^(1<<i-1)^(1<<j-1)^(1<<x-1))+1);
		}
	return f[s];
}
int main(){
	int n=read(),x,y,z,state=0,ans=0;
	for(register int i=1;i<n;++i)x=read()+1,y=read()+1,z=read(),a[x]^=z,a[y]^=z;
	for(register int i=1;i<=n;++i)++tax[a[i]];
	for(register int i=1;i<=15;++i){
		ans+=tax[i]>>1;
		if(tax[i]&1)state|=1<<i-1;
	}
	memset(f,-1,sizeof f),f[0]=0;
	printf("%d\n",ans+dfs(state));
}
```

