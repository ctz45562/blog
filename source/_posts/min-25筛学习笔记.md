---
title: min_25筛学习笔记
date: 2020-07-16 15:52:53
categories: 学习笔记
tags:
    - 数论
    - min_25筛
keywords: min_25筛
description:
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.2.4/min25sxxbj.jpg
mathjax: true
---

前几天看贴吧才知道弃坑不久后朝奈就觉醒了。

于是回去蝗了一波顺便抽了个爱心厨娘（真香）

<!--more-->

---

# 前言

$min\\_25$筛是一种在亚线性复杂度内计算积性函数单个前缀和的算法。

我也不知道为什么要学这个。反正NOI就是去玩玩，所以就随便学点算法玩玩吧。

---

# 抄袭来源

> https://www.cnblogs.com/cjyyb/p/9185093.html
>
> https://www.luogu.com.cn/problem/solution/P5325
> 
> https://challestend.github.io/min-25-sieve-learning-notes/

---

# #define

$P$：质数集合

$P_i$：第$i$小的质数。特别的，$P_0=0$

$p$：若未特别说明，$p$均代表质数

$minp(i)$：$i$的最小质因子

---

# min_25筛

## 适用条件

- $f(x)$为**积性函数**
- $f(p)$可以用关于$p$的**简单多项式**表示
- $f(p^k)$能快速算出

## [模板](https://www.luogu.com.cn/problem/P5325)

先绕个弯，不妨设$S(n,k)=\sum\limits_{i=1}^n[minp(i)>P_k]f(i)$

答案就是$S(n,0)+1$（$+1$是因为$1$没有最小质因子，不会被算进$S(n,0)$里，要额外加上）

考虑怎么求$S(n,k)$。分个类，分别求质数和合数的贡献：

### 质数部分

因为$minp(p)=p$，所以$\sum\limits_{i=1}^n[i\in P\land minp(i)>P_k]f(i)=\sum\limits_{i=1}^n[i\in P]f(i)-\sum\limits_{i=1}^kf(P_i)$

光看前面的$\sum\limits_{i=1}^n[i\in P]f(i)$，后面的$\sum\limits_{i=1}^kf(P_i)$最后会提到可以预处理。

根据$min\\_25$筛的适用条件：$f(p)$可以用关于$p$的简单多项式表示。如果我们计算出$\sum\limits_{i=1}^n[i\in P]i^k$，就能用$\sum\limits_{k=0}^\infty F_k\sum\limits_{i=1}^n[i\in P]i^k$得出$f(i)$的和（其中，$f(p)=\sum\limits_{k=0}^\infty F_kp^k$）。

本题中$f(p)=p^2-p$，符合条件。接下来只需要考虑怎么计算$\sum\limits_{i=1}^n[i\in P]i^k$

再绕个弯，不妨设$g_k(n,j)=\sum\limits_{i=1}^n[i\in P\lor minp(i)>P_j]i^k$

初始值$g_k(n,0)=\left(\sum\limits_{i=1}^ni^k\right)-1$（$-1$是因为这两个条件$1$都不符合）

考虑转移：$g_k(n,j)$相较于$g_k(n,j-1)$，去掉了那些最小质因子等于$P_j$且不为质数的数。

- 如果$P_j^2>n$，显然不会有合数的最小质因子等于$P_j$，于是$g_k(n,j)=g_k(n,j-1)$

- 如果$P_j^2\le n$，把最小质因子$P_j$提出来，有$P_j^k$的贡献，让剩下的数最小质因子不小于$P_j$即可，即$g_k\left(\dfrac{n}{P_j},j-1\right)$。但是由于$g_k$的定义中有一条$i\in P$，小于$P_j$的质数不应该被算上，所以要减去$g_k(P_{j-1},j-1)$，显然$g_k(P_{j-1},j-1)=\sum\limits_{i=1}^{j-1}P_i^k$

综上，$g_k(n,j)=\begin{cases}g_k(n,j-1)&P_j^2>n\\g_k(n,j-1)-P_j^k\left(g_k\left(\dfrac{n}{P_j},j-1\right)-\sum\limits_{i=1}^{j-1}P_i^k\right)&P_j^2\le n\end{cases}$

而$g_k(n,+\infty)=\sum\limits_{i=1}^n[i\in P]i^k$，即为我们最初想要的答案。当然这个转移需要优化：

- 由于只有$P_j^2\le n$时会进行转移，所以只需要用到不大于$\sqrt n$的质数，$\sum\limits_{i=1}^jP_i^k$也可以预处理了。
- 注意到整个转移中的$n$只出现过$\left\lfloor\dfrac{n}{i}\right\rfloor$的形式，根据整除分块的性质这个值只有$O(\sqrt n)$个。利用一个$trick$：$i\le\sqrt n$时存到下标$i$里；$i>\sqrt n$时存到下标$\sqrt n+\dfrac{n}{i}$里。同时我们只想知道$g_k(n,+\infty)$，第二维可以滚掉。这样时空都能被大大优化。

至此，质数部分就计算完了，复杂度是$O(\dfrac{n^{\frac{3}{4}}}{\log n})$（不会证）

### 合数部分

类比上文$g_k$的转移，枚举最小质因子，假设为$P_i$。因为有$minp(i)>P_k$的限制，所以要从第$k+1$个质数开始枚举。但不同于$g_k$的是，这不是完全积性函数，$f(P_i)$不能随便提出来，所以还要枚举其次数$j$，把$f(P_i^j)$整个提出来，这样就符合积性函数的性质了。

接下来要保证的就是剩下的数最小质因子**大于**$P_i$（注意，这里是把$P_i^j$整个提出来了，所以剩下的数里不能有$P_i$），也就是$S\left(\dfrac{n}{P_i^j},i\right)$。不过这里有一个问题：如果$j>1$，$f(P_i^j)$也应该被算进$S(n,k)$里（$j=1$在质数部分算过了），也就是$f(P_i^j)\times f(1)$。但是$S$并不会计算$1$的贡献，所以这里应该是$S\left(\dfrac{n}{P_i^j},i\right)+[j>1]$。

根据质数部分的经验，只有$P_i^2\le n$时才会有贡献，所以还是只需要用到不大于$\sqrt n$的质数。

于是我们得到了合数部分的贡献：$\sum\limits_{i>k,P_i^2\le n,P_i^j\le n}f(P_i^j)\left(S(\dfrac{n}{P_i^j},i)+[j>1]\right)$

根据$min\\_25$筛的条件，$f(P_i^j)$可以快速计算，或者能从$P_i$递推到$P_i^j$，这部分就能快速递归计算了。

### 整合

把式子的完全体写出来：

$S(n,k)=\sum\limits_{k=0}^\infty F_kg_k(n,+\infty)-\sum\limits_{i=1}^kf(P_i)+\sum\limits_{i>k,P_i^2\le n,P_i^j\le n}f(P_i^j)\left(S(\dfrac{n}{P_i^j},i)+[j>1]\right)$

自始至终$n$都只取到过$\left\lfloor\dfrac{n}{i}\right\rfloor$，$g_k$也恰好只计算了$\left\lfloor\dfrac{n}{i}\right\rfloor$处的值；而由于只用到了$\sqrt n$以内的质数，$k$不会很大，$\sum\limits_{i=1}^kf(P_i)$也可以预处理了。这样这个式子就没有问题了。

复杂度是$O(\dfrac{n^{\frac{3}{4}}}{\log n})$（不会证），甚至不用记忆化。

### 代码

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 100005
#define inf 0x3f3f3f3f

const int mod = 1e9 + 7;
const int inv2 = 500000004;
const int inv6 = 166666668;

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
long long n;
int f[maxn],g[2][maxn<<1],prime[maxn>>2],pw[2][maxn],cnt,sq;
bool is[maxn];
inline int qm(int x){return x>=mod?x-mod:x;}
inline int id(long long x){return x>sq?sq+n/x:x;}
int S(long long N,int k){
	if(prime[k]>=N)return 0;
	int ans=qm(qm(g[1][id(N)]+mod-g[0][id(N)])+mod-f[k]);
	for(register int i=k+1;i<=cnt&&1ll*prime[i]*prime[i]<=N;++i)
		for(long long j=prime[i];j<=N;j*=prime[i]){
			int b=j%mod;
			ans=qm(ans+1ll*b*(b-1)%mod*qm(S(N/j,i)+(j>prime[i]))%mod);
		}
	return ans;
}
int main(){
	for(register int i=2;i<maxn;++i){
		if(!is[i])prime[++cnt]=i,pw[0][cnt]=qm(pw[0][cnt-1]+i),pw[1][cnt]=qm(pw[1][cnt-1]+1ll*i*i%mod),f[cnt]=qm(f[cnt-1]+1ll*i*(i-1)%mod);
		for(register int j=1;j<=cnt&&i*prime[j]<maxn;++j){
			is[i*prime[j]]=1;
			if(i%prime[j]==0)break;
		}
	}
	n=read<long long>(),sq=sqrt(n);
	for(long long l=1,r;l<=n;l=n/(n/l)+1){
		int x=n/l%mod,k=id(n/l);
		g[0][k]=qm(1ll*x*(x+1)%mod*inv2%mod+mod-1);
		g[1][k]=qm(1ll*x*(x+1)%mod*((x<<1)+1)%mod*inv6%mod+mod-1);
	}
	for(register int i=1;i<=cnt;++i)
		for(long long l=1,r;l<=n&&1ll*prime[i]*prime[i]<=n/l;l=n/(n/l)+1){
			int k=id(n/l);
			g[0][k]=qm(g[0][k]+mod-1ll*prime[i]*qm(g[0][id(n/l/prime[i])]+mod-pw[0][i-1])%mod);
			g[1][k]=qm(g[1][k]+mod-1ll*prime[i]*prime[i]%mod*qm(g[1][id(n/l/prime[i])]+mod-pw[1][i-1])%mod);
		}
	printf("%d\n",qm(S(n,0)+1));
}
```

---

# 水题

## [【模板】杜教筛](https://www.luogu.com.cn/problem/P4213)

根据小学二年级数学：

$\varphi(p)=p-1,\varphi(p^k)=p^k-p^{k-1}$

$\mu(p)=-1,\mu(p^k)=0$

这样就可以做了。而且由于$\mu(p^k)=0$，筛$\mu$的合数部分甚至不用枚举$P_i$的指数。

## [简单的函数](https://loj.ac/problem/6053)

$f(p^k)$表达式已经明确告诉你了，只需要考虑$f(p)=p\oplus 1$的表示。而$p$为偶数时$f(p)=p+1$，否则$f(p)=p-1$。

因为所有的质数里只有$2$是偶数，所以可以得到：

$f(p)=\begin{cases}3&p=2\\p-1&ohterwise\end{cases}$

所以：

$\sum\limits_{i=1}^n[i\in P]f(i)=\begin{cases}0&n<2\\g_1(n,+\infty)-g_0(n,+\infty)+2&n\ge2\end{cases}$

## [梦中的数论](https://loj.ac/problem/6682)

显然答案是$\sum\limits_{i=1}^nC_{d(i)}^2$，$d(i)$为$i$约数个数。

而$C_{d(i)}^2=\dfrac{d(i)(d(i)-1)}{2}=\dfrac{d^2(i)-d(i)}{2}$不积性，但是$d^2(i)$和$d(i)$是积性的。

$d(p^k)=k+1$，筛出$d^2(i)$和$d(i)$作差除以$2$即可。

## [DIVCNTK](https://www.luogu.com.cn/problem/SP34096)

如果你跑去spoj看原题的话，其实只有$n\le 10^4$时$T\le 10^4$，而$n=10^10$时$T\le 5$。

然后$d(i^k)$积性，$d((p^c)^k)=kc+1$，没了。

三倍经验：[DIVCNT2](https://www.luogu.com.cn/problem/SP20173) [DIVCNT3](https://www.luogu.com.cn/problem/SP20174)

有点卡常。

## [Sanrd](http://uoj.ac/problem/188)

[题解](/2020/07/19/UOJ-188-【UR-13】Sanrd/)