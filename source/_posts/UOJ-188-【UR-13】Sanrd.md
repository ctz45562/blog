---
title: 'UOJ #188.【UR #13】Sanrd'
date: 2020-07-19 11:12:39
categories: 题解
tags:
    - 数论
    - min_25筛
keywords: Sanrd
description:
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.2.5/sanrd.jpg
mathjax: true
---

[传送门](http://uoj.ac/problem/188)

第一次在uoj上交题？

<!--more-->

把题面翻译成人话：求$[l,r]$中所有数的**可重**次大质因子之和。

设$i$的次大质因子为$f(i)$。$f(i)$虽然不积性，但还是可以考虑$min\\_25$筛的思想。

设$S(n,k)=\sum\limits_{i=1}^n[minp(i)>P_k]f(i)$，答案就是$S(r,0)-S(l-1,0)$。

因为质数和$1$的$f$都是$0$，所以只需要考虑合数的贡献，枚举最小质因子及其次数$P_i^j$。$P_i^j$有两种贡献：

- $P_i$就是次小质因子：此时因子中除了$P_i^j$，就只会有一个大于$P_i$的质数$P_x$且$P_i^jP_x\le n$，也就是$\max\left\{g_0(\dfrac{n}{P_i^j},+\infty)-i,0\right\}$。而如果$j>1$，还要算上$P_i^j$自己。这部分贡献就是$P_i\times\left(\max\left\{g_0(\dfrac{n}{P_i^j},+\infty)-i,0\right\}+[j>1]\right)$
- $P_i$不是次小质因子：这部分就和$P_i$没啥关系了，因为最小质因子为$P_i^j$，次小质因子一定在上面，贡献为$S(\dfrac{n}{P_i^j},i)$

综上，$S(n,k)=\sum\limits_{i>k,P_i^2\le n,P_i^j\le n}P_i\times\left(\max\left\{g_0(\dfrac{n}{P_i^j},+\infty)-i,0\right\}+[j>1]\right)+S(\dfrac{n}{P_i^j},i)$

而且因为只需要考虑合数，当$P_k^2>n$的时候就可以返回$0$了。

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
template<typename T>
inline T read(){
	T x=0;
	int y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
long long n,g[maxn<<1];
int prime[maxn>>2],sq,cnt;
bool is[maxn];
inline int id(long long x){return x<=sq?x:sq+n/x;}
long long S(long long n,int k){
	if(1ll*prime[k]*prime[k]>n)return 0;
	long long ans=0;
	for(register int i=k+1;i<=cnt&&1ll*prime[i]*prime[i]<=n;++i){
		long long base=prime[i];
		for(register int j=1;base<=n;++j,base*=prime[i])
			ans+=(max(g[id(n/base)]-i,0ll)+(j>1))*prime[i]+S(n/base,i);
	}
	return ans;
}
long long solve(long long N){
	n=N,sq=sqrt(n);
	for(long long l=1;l<=n;l=n/(n/l)+1)g[id(n/l)]=n/l-1;
	for(register int i=1;i<=cnt;++i)
		for(long long l=1;1ll*prime[i]*prime[i]<=n/l;l=n/(n/l)+1){
			int k=id(n/l);
			g[k]-=g[id(n/l/prime[i])]-i+1;
		}
	return S(n,0);
}
int main(){
	for(register int i=2;i<maxn;++i){
		if(!is[i])prime[++cnt]=i;
		for(register int j=1;j<=cnt&&i*prime[j]<maxn;++j){
			is[i*prime[j]]=1;
			if(i%prime[j]==0)break;
		}
	}
	long long l=read<long long>(),r=read<long long>();
	printf("%lld\n",solve(r)-solve(l-1));
}
```