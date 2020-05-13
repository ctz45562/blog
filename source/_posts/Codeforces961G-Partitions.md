---
title: Codeforces961G Partitions
date: 2019-12-11 10:02:48
categories: 题解
tags:
    - 数论
    - 组合数学
    - 斯特林数
keywords: Partitions
description: 「有生之年」系列节目之——跟活的成爷一起上数学课
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/partitions.jpg
mathjax: true
---

[传送门](https://www.luogu.com.cn/problem/CF961G)

**「有生之年」系列节目之——跟活的成爷一起上数学课**{% emoji_coda xiaodianshi/taitaixihuan %}

<!--more-->

考虑每个物品的贡献。枚举该物品在所在的集合大小，易得答案为：

$\sum\limits_{s=1}^nw_s\sum\limits_{i=1}^niC_{n-1}^{i-1}\begin{Bmatrix}n-i\\k-1\end{Bmatrix}$

求出单列第二类斯特林数就行了。再考察一下模数。

$10^9+7$，好的不是$NTT$模数呢~~而且我也不会求单列第二类斯特林数~~

于是开始~~抄题解~~推式子，暴力用第二类斯特林数通项公式展开：

$\sum\limits_{i=1}^niC_{n-1}^{i-1}\begin{Bmatrix}n-i\\k-1\end{Bmatrix}$

$=\sum\limits_{j=0}^{k-1}\sum\limits_{i=1}^niC_{n-1}^{i-1}\dfrac{(-1)^j(k-1-j)^{n-i}}{j!(k-1-j)!}$

$=\sum\limits_{j=0}^{k-1}\dfrac{(-1)^j}{j!(k-1-j)!}\sum\limits_{i=1}^n(i-1+1)C_{n-1}^{i-1}(k-1-j)^{n-i}$

$=\sum\limits_{j=0}^{k-1}\dfrac{(-1)^j}{j!(k-1-j)!}\left(\sum\limits_{i=1}^n(i-1)C_{n-1}^{i-1}(k-1-j)^{n-i}+\sum\limits_{i=1}^nC_{n-1}^{i-1}(k-1-j)^{n-i}\right)$

根据$mC_n^m=nC_{n-1}^{m-1}$和二项式定理，有：

$=\sum\limits_{j=0}^{k-1}\dfrac{(-1)^j}{j!(k-1-j)!}\left((n-1)\sum\limits_{i=1}^nC_{n-2}^{i-2}(k-1-j)^{n-i}+(k-j)^{n-1}\right)$

$=\sum\limits_{j=0}^{k-1}\dfrac{(-1)^j}{j!(k-1-j)!}((n-1)(k-j)^{n-2}+(k-j)^{n-1})$

$=\sum\limits_{j=0}^{k-1}\dfrac{(-1)^j}{j!(k-1-j)!}(n-1+k-j)(k-j)^{n-2}$

就能$O(k\log n)$做了。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 200005
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
int inv[maxn];
inline int quickpow(int x,int y=mod-2){
	if(y<0)return quickpow(quickpow(x,-y));
	int ans=1;
	while(y){
		if(y&1)ans=1ll*ans*x%mod;
		x=1ll*x*x%mod,y>>=1;
	}
	return ans;
}
int main(){
	int n=read(),k=read(),sum=0,ans=0,fac=1;
	for(register int i=1;i<=n;++i)(sum+=read())%=mod,fac=1ll*fac*i%mod;
	inv[n]=quickpow(fac);
	for(register int i=n-1;~i;--i)inv[i]=1ll*inv[i+1]*(i+1)%mod;
	for(register int i=0;i<k;++i)(ans+=(i&1?-1ll:1ll)*inv[i]*inv[k-i-1]%mod*(n-1+k-i)%mod*quickpow(k-i,n-2)%mod)%=mod;
	printf("%d\n",(1ll*sum*ans%mod+mod)%mod);
}
```