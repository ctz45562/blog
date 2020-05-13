---
title: 'USACO-13OPEN 照片Photo'
date: 2019-04-10 10:35:31
categories: 题解
tags:
	- 动态规划
	- 单调队列
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/zpphoto.jpg
keywords: 照片Photo
description: 思路甚妙的DP（蒟蒻并不会差分约束QwQ）
---

[传送门](https://www.luogu.org/problemnew/show/P3084)

思路甚妙的$DP$（蒟蒻并不会差分约束$QwQ$）

<!--more-->

设$f(i)$为只考虑前$i$个，且第$i$头牛为斑点牛的最大值。

初始状态显然为$f(0)=0$。

转移：

1. 每个区间只有一头斑点牛，第$i$头为斑点牛，则所有覆盖$i$的区间都不能转移，记$rr[i]$为**覆盖**点$i$的所有区间中最靠左的左端点。
2. 每个区间必须有一头斑点牛，所有不覆盖$i$的区间必须有斑点牛，记$ll[i]$为**不覆盖**点$i$的区间中最靠右的左端点。

就有转移方程$f(i)=\max \{f(j)+1\}(ll[i]\le j \le rr[i])$

记所有区间中最靠右的左端点为$ml$，答案即为$\max \{f(i)\}(ml\le i \le n)$。（最靠右的区间必须有斑点牛）

可以用单调队列优化，时间复杂度$O(n)​$。

对于预处理$ll[i],rr[i]​$同样可以$O(n)​$实现。对于每个区间$[L,R]​$，将$rr[L]​$与$L​$取$min​$；将$ll[R+1]​$与$L​$取$max​$，然后$ll​$正着扫一遍取前缀最大值，$rr​$倒着扫一遍取后缀最小值。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 200005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int ll[maxn],rr[maxn],f[maxn],line[maxn],head,tail;
inline void push(int p){
	if(f[p]==-1)return;
	while(head<=tail&&f[line[tail]]<=f[p])--tail;
	line[++tail]=p;
}
int main(){
	int n=read(),m=read(),l,r,ml=1,poi=-1,ans=-1;
	for(register int i=1;i<=n;++i)
		rr[i]=i;
	for(register int i=1;i<=m;++i)
		l=read(),r=read(),ll[r+1]=max(ll[r+1],l),rr[r]=min(rr[r],l),ml=max(ml,l);
	for(register int i=1;i<=n;++i)
		ll[i]=max(ll[i],ll[i-1]);
	for(register int i=n-1;i;--i)
		rr[i]=min(rr[i],rr[i+1]);
	memset(f,-1,sizeof f);
	f[0]=0,head=1;
	for(register int i=1;i<=n;++i){
		while(poi<rr[i]-1)++poi,push(poi);
		while(head<=tail&&line[head]<ll[i])++head;
		if(head<=tail)f[i]=f[line[head]]+1;
	}
	for(register int i=ml;i<=n;++i)
		ans=max(ans,f[i]);
	printf("%d\n",ans);
}

```

