---
title: '洛谷 P1829 [国家集训队]Crash的数字表格'
date: 2019-07-10 16:32:59
categories: 题解
tags: 
	- 数论
	- 莫比乌斯反演
description: 颓莫比乌斯反演题好爽啊！
keywords: Crash的数字表格
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/crashdszbg.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problemnew/show/P1829)

**颓**莫比乌斯反演题**好爽啊**！

<!--more-->

小学数论知识：$lcm(i,j)=\dfrac{ij}{gcd(i,j)}$

以下规定$n<m$。

$\sum\limits_{i=1}^n\sum\limits_{j=1}^mlcm(i,j)$

$=\sum\limits_{i=1}^n\sum\limits_{j=1}^m\dfrac{ij}{gcd(i,j)}$

套路枚举$gcd$：

$=\sum\limits_{d=1}^n\sum\limits_{i=1}^n\sum\limits_{j=1}^m\dfrac{ij[gcd(i,j)=d]}{d}$

套路枚举$d$的倍数：

$=\sum\limits_{d=1}^n\sum\limits_{i=1}^{\lfloor\frac{n}{d}\rfloor}\sum\limits_{j=1}^{\lfloor\frac{m}{d}\rfloor}ijd[gcd(i,j)=1]$

$=\sum\limits_{d=1}^n\sum\limits_{i=1}^{\lfloor\frac{n}{d}\rfloor}\sum\limits_{j=1}^{\lfloor\frac{m}{d}\rfloor}ijd\sum\limits_{a|gcd(i,j)}\mu(a)$

$=\sum\limits_{d=1}^nd\sum\limits_{a=1}^{\lfloor\frac{n}{d}\rfloor}\mu(a)\sum\limits_{i=1}^{\lfloor\frac{n}{d}\rfloor}\sum\limits_{j=1}^{\lfloor\frac{m}{d}\rfloor}ij[a|i][a|j]$

套路枚举$a$的倍数：

$=\sum\limits_{d=1}^nd\sum\limits_{a=1}^{\lfloor\frac{n}{d}\rfloor}\mu(a)\sum\limits_{i=1}^{\lfloor\frac{n}{ad}\rfloor}ia\sum\limits_{j=1}^{\lfloor\frac{m}{ad}\rfloor}ja$

$=\sum\limits_{d=1}^nd\sum\limits_{a=1}^{\lfloor\frac{n}{d}\rfloor}\mu(a)a^2\sum\limits_{i=1}^{\lfloor\frac{n}{ad}\rfloor}i\sum\limits_{j=1}^{\lfloor\frac{m}{ad}\rfloor}j$

设$g(n)=\sum\limits_{i=1}^ni$（就是自然数前$n$项和）,$f(n,m)=\sum\limits_{i=1}^n\mu(i)i^2g(\lfloor\dfrac{n}{i}\rfloor)g(\lfloor\dfrac{m}{i}\rfloor)$

这个$f(n,m)$显然可以预处理$\mu(i)i^2$后整除分块。

我们求的就成了$\sum\limits_{d=1}^ndf(\lfloor\dfrac{n}{d}\rfloor,\lfloor\dfrac{m}{d}\rfloor)$

又可以整除分块了。

整除分块套整除分块复杂度是$O(n^{\frac{3}{4}})$的。

然后发现了一个有趣的东西：

暴力计算$f(n,m)$是$O(n)$的。

设$F(n)$为$\lfloor\dfrac{n}{i}\rfloor(i\le n)$所有不同取值的和，它近似于在$f(n,m)$不整除分块时的整个算法复杂度。

经过计算在$F(1e7)\approx 9e7$。试着改了一下不吸氧就过了。。。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 10000001
#define inf 0x3f3f3f3f

const int mod = 20101009;

using namespace std;

inline int read(){
    int x=0,y=0;
    char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
    while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
    return y?-x:x;
}
int mu[maxn],sum[maxn],psum[maxn],prime[maxn>>3],cnt;
bool is[maxn];
inline int f(int n,int m){
    if(n>m)swap(n,m);
    int ans=0;
    for(register int l=1,r;l<=n;l=r+1){
        r=min(n,min(n/(n/l),m/(m/l)));
        (ans+=1ll*(psum[r]-psum[l-1])*sum[n/l]%mod*sum[m/l]%mod)%=mod;
    }
    return ans;
}
int main(){
    mu[1]=is[1]=sum[1]=psum[1]=1;
    for(register int i=2;i<maxn;++i){
        if(!is[i])prime[++cnt]=i,mu[i]=-1;
        sum[i]=(sum[i-1]+i)%mod,psum[i]=(psum[i-1]+1ll*i*i*mu[i]%mod)%mod;
        for(register int j=1;j<=cnt&&i*prime[j]<maxn;++j){
            is[i*prime[j]]=1;
            if(i%prime[j]==0){mu[i*prime[j]]=0;break;}
            mu[i*prime[j]]=-mu[i];
        }
    }
    int n=read(),m=read(),ans=0;
    if(n>m)swap(n,m);
    for(register int l=1,r;l<=n;l=r+1){
        r=min(n,min(n/(n/l),m/(m/l)));
        (ans+=1ll*(sum[r]-sum[l-1])*f(n/l,m/l)%mod)%=mod;	
    }
    printf("%d\n",(ans+mod)%mod);
}
```

$update$：

由于$O(n^{\frac{3}{4}})$足以通过此题，所以当时没继续往下推。然而还是很容易优化到$O(\sqrt{n})$的。

$\sum\limits_{d=1}^nd\sum\limits_{a=1}^{\lfloor\frac{n}{d}\rfloor}\mu(a)a^2g(\left\lfloor\dfrac{n}{ad}\right\rfloor)g(\left\lfloor\dfrac{m}{ad}\right\rfloor)$

设$T=ad$，有$d|T$。改为枚举$T$：

$=\sum\limits_{d=1}^nd\sum\limits_{T=1}^n[d|T]\mu(\dfrac{T}{d})\left(\dfrac{T}{d}\right)^2g(\left\lfloor\dfrac{n}{T}\right\rfloor)g(\left\lfloor\dfrac{m}{T}\right\rfloor)$

$=\sum\limits_{T=1}^ng(\left\lfloor\dfrac{n}{T}\right\rfloor)g(\left\lfloor\dfrac{m}{T}\right\rfloor)T\sum\limits_{d|T}\mu(\dfrac{T}{d})\dfrac{T}{d}$

$=\sum\limits_{T=1}^ng(\left\lfloor\dfrac{n}{T}\right\rfloor)g(\left\lfloor\dfrac{m}{T}\right\rfloor)T\sum\limits_{d|T}\mu(d)d$

设$F(T)=\sum\limits_{d|T}\mu(d)d$。可以证明$F$是积性函数（~~懒得写了~~挂个[有证明的链接](https://www.luogu.org/blog/An-Amazing-Blog/mu-bi-wu-si-fan-yan-ji-ge-ji-miao-di-dong-xi)吧）

对于质数$p$，$F(p)=1-p$，$F(p^{k+1})=F(p^k)$，就能线筛了。

复杂度降为$O(\sqrt{n})$。
