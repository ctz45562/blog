---
title: Codeforces449D Jzzhu and Numbers
date: 2019-10-22 16:39:35
categories: 题解
tags:
	- 数论
	- 高维前缀和
	- 容斥
keywords: Jzzhu and Numbers
description: 高维前缀和真是挺有意思的呵。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/jzzhuandnumbers.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF449D)

高维前缀和真是挺有意思的呵{% emoji_coda xiaodianshi/xiao %}。

<!--more-->

直接$DP$只能$O(na_i)$，咱也不会$FWT$，考虑容斥。

设$f(S)$为选若干个数按位与结果为$S$的超集的方案数。

容斥一下，答案就是$\sum\limits_{S}(-1)^{|S|}f(S)$。

接下来求$f(S)$。

设$g(S)$为二进制为$S$的超集的$a_i$的个数。

显然从$g(S)$中任取几个数按位与，结果也一定是$S$的超集。

去掉空集，$f(S)=2^{g(S)}-1$。

接下来求$g(S)$。

初始化$++g(a_i)$。然后做个前缀和，$g(S)=\sum\limits_{S\subseteq T}g(T)$。

高维前缀和优化，不过和我刚学的有点区别，因为$S,T$位置倒过来了，所以实际上是个后缀和，魔改一下就行了。

复杂度$O(a_i\log a_i)$。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 1000005
#define inf 0x3f3f3f3f

const int mod = 1e9 + 7;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int tax[1<<20];
inline int pow1(int x){return x&1?-1:1;}
inline int pop_count(int x){
	int ans=0;
	while(x)++ans,x-=(x&-x);
	return ans;
}
inline int quickpow(int y){
	int ans=1,x=2;
	while(y){
		if(y&1)ans=1ll*ans*x%mod;
		x=1ll*x*x%mod,y>>=1;
	}
	return ans;
}
int main(){
	int n=read(),ans=0;
	for(register int i=1;i<=n;++i)++tax[read()];
	for(register int i=0;i<20;++i)
		for(register int j=0;j<1<<20;++j)
			if(!(j>>i&1))tax[j]+=tax[j|(1<<i)];
	for(register int i=0;i<1<<20;++i)
		(ans+=pow1(pop_count(i))*(quickpow(tax[i])-1))%=mod;
	printf("%d\n",(ans+mod)%mod);
}

```

