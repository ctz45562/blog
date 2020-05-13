---
title: '洛谷 P2523 [HAOI2011]Problem c'
date: 2019-06-24 16:34:31
categories: 题解
tags: 
	- 动态规划
	- 组合数学
description: 妙啊！
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/problemc.jpg
keywords: Problem c
mathjax: true
---

[传送门](https://www.luogu.org/problemnew/show/P2523)

妙啊！

<!--more-->

先考虑不合法的方案长啥样：

设$sum_i$为$a_i\ge i$的个数。如果$\exists i,sum_i>n-i+1$，则该方案不合法。

这个很好理解，对于某种方案来说，会有$sum_i$个人坐到$[i,n]$的位置，而$[i,n]$最多容纳$n-i+1$个人，即$sum_i\le n-i+1$。

再设$f(i,j)$为**未确定位置**的人中，$a_i\ge i$的有$j$个人的方案数，$cnt_i$为**已确定位置**的人中，$a_i\ge i$的个数。

初始状态$f(n+1,0)=1$。

转移我们枚举有多少人$a_i=i$。

则$f(i,j)=\sum\limits_{k=0}^jf(i+1,j-k)$

因为入座的时候是有次序的，还要再$C_j^k$枚举一下哪些人$a_i=i$。

即$f(i,j)=\sum\limits_{k=0}^jf(i+1,j-k)C_k^j$

要满足$sum_i\le n-i+1$，已经有$cnt_i$个人被钦定为$a_i\ge i$，那么仅考虑未确定位置的人的$sum$时，有$sum_i-cnt_i\le n-i+1$

也就是说$j\in [0,n-i+1-cnt_i]$。

显然$sum_1$就是总人数，答案就是$f(1,n-m)$。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 305
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int mod,f[maxn][maxn],sum[maxn],c[maxn][maxn];
int main(){
	int t=read(),n,m;
	c[0][0]=1;
	while(t--){
		n=read(),m=read(),mod=read();
		for(register int i=1;i<=n;++i){
			c[i][0]=1;
			for(register int j=1;j<=i;++j)
				c[i][j]=(c[i-1][j]+c[i-1][j-1])%mod;
		}
		memset(f,0,sizeof f);
		memset(sum,0,sizeof sum);
		for(register int i=1;i<=m;++i)read(),++sum[read()];
		for(register int i=n;i;--i){
			sum[i]+=sum[i+1];
			if(sum[i]>n-i+1)goto Bad_Option;
		}
		f[n+1][0]=1;
		for(register int i=n;i;--i)
			for(register int j=0;j<=n-i+1-sum[i];++j)
				for(register int k=0;k<=j;++k)
					(f[i][j]+=1ll*c[j][k]*f[i+1][j-k]%mod)%=mod;
		printf("YES %d\n",f[1][n-m]);
		continue;
Bad_Option:
		puts("NO");
	}
}
```

