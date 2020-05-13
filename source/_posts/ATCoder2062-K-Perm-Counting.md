---
title: ATCoder2062 ~K Perm Counting
date: 2019-10-24 19:58:28
categories: 题解
tags:
	- 动态规划
	- 容斥
keywords: ~K Perm Counting
description: 难得能独立推出容斥题好激动啊。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/kpermcounting.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/AT2062)

难得能独立推出容斥题好激动啊{% emoji_coda xiaodianshi/haixiu %}。

<!--more-->

这么鬼畜的条件显然不能直接$DP$，考虑容斥。

设$f(i)$为至少$i$个位置不满足条件的方案数。

这玩意还是不好直接求。

$|P_i-i|\neq k\leftrightarrow P_i\neq i+k \lor P_i\neq i-k$

一个位置有两种取值是不合法的。

当两个位置$i,j$同时不合法且$P_i=P_j$时，必然满足$i+k=j-k$。

也就是说$i$与$i+2k$存在一个共同的不合法值$i+k$。

把这些不合法值连起来（假设$k=2$）：

![](\images\kpermcounting-1.png)

这时我们可以对每条这样的链$DP$。

设$g(i,j,0/1/2)$为考虑到$i$，$i$所在的链选了$j$个不合法值，$i$选择了$i-k/i+k$或不选。

普及转移：

$g(i,j,0)=g(i-2k,j-1,0)+g(i-2k,j-1,2)$

$g(i,j,1)=g(i-2k,j-1,0)+g(i-2k,j-1,1)+g(i-2k,j-1,2)$

$g(i,j,2)=g(i-2k,j,0)+g(i-2k,j,1)+g(i-2k,j,2)$

考虑合并所有链，做个背包就行了：

$f(i)=\sum\limits_{j=1}^i f(i-j)\times (g(k,j,0)+g(k,j,1)+g(k,j,2))(k\in [\max\{n-2k+1,1\},n])$

最后容斥一波，由于求的$f(i)$是仅考虑选不合法位置的方案数，还要给剩下位置随机钦定。

$ans=\sum\limits_{i=0}^n(-1)^if(i)(n-i)!$

我的写法不能处理$k=0$的情况要特判，这时其实就是错排。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 2005
#define inf 0x3f3f3f3f

const int mod = 924844033;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int f[maxn],c[maxn][maxn],g[maxn][maxn][3],fac[maxn]={1},n,m,ans;
inline long long pow1(int x){return x&1?-1ll:1ll;}
int main(){
	n=read(),m=read();
	for(register int i=1;i<=n;++i)fac[i]=1ll*fac[i-1]*i%mod;
	if(!m){
		c[0][0]=1;
		for(register int i=1;i<=n;++i){
			c[i][0]=1;
			for(register int j=1;j<=i;++j)
				c[i][j]=(c[i-1][j-1]+c[i-1][j])%mod;
		}
		for(register int i=0;i<=n;++i)
			(ans+=pow1(i)*c[n][i]*fac[n-i]%mod)%=mod;
		printf("%d\n",(ans+mod)%mod);
		return 0;
	}
	for(register int i=1;i<=min(m<<1,n);++i){
		g[i][0][2]=1;
		if(i-m>0)g[i][1][0]=1;
		if(i+m<=n)g[i][1][1]=1;
	}
	for(register int i=(m<<1)+1;i<=n;++i){
		int x=i-(m<<1);
		for(register int j=0;j<=n;++j){
			if(j&&i-m>0)g[i][j][0]=(g[x][j-1][0]+g[x][j-1][2])%mod;
			if(j&&i+m<=n)g[i][j][1]=((g[x][j-1][0]+g[x][j-1][1])%mod+g[x][j-1][2])%mod;
			g[i][j][2]=((g[x][j][0]+g[x][j][1])%mod+g[x][j][2])%mod;
		}
	}
	f[0]=1;
	int sum=0,cnt;
	for(register int i=max(n-(m<<1)+1,1);i<=n;++i){
		cnt=(i-1)/(m<<1)+1,sum+=cnt;
		for(register int k=1;k<=cnt;++k)

		for(register int j=sum;j;--j)
			for(register int k=min(j,cnt);k;--k)
				(f[j]+=1ll*f[j-k]*((g[i][k][0]+g[i][k][1])%mod+g[i][k][2])%mod)%=mod;
	}
	for(register int i=0;i<=n;++i)
		(ans+=pow1(i)*f[i]*fac[n-i]%mod)%=mod;
	printf("%d\n",(ans+mod)%mod);
}

```

