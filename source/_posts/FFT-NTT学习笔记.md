---
title: FFT/NTT学习笔记
date: 2019-12-07 10:12:52
categories: 学习笔记
tags:
    - 数论
    - FFT/NTT
    - 多项式
keywords: FFT/NTT
description: 大声大声！熟练熟练！
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/fftnttxxbj.jpg
mathjax: true
---

> 大声大声！熟练熟练！

~~又又不要打我~~

<!--more-->

---

# 前言

历经四个月终于开新算法了。

最近越来越懒了，而且抄袭来源讲的炒鸡详细就只放模板和水题了。

---

# 抄袭来源

> https://www.luogu.com.cn/blog/command-block/fft-xue-xi-bi-ji
> https://www.luogu.com.cn/blog/command-block/ntt-yu-duo-xiang-shi-quan-jia-tong#

---

# 板子

## FFT

``` cpp
#define maxn 2100005
const double pi = acos(-1)*2;
struct cp{
    double x,y;
    cp(double a=0,double b=0){x=a,y=b;}
    cp operator + (const cp &a){return cp(x+a.x,y+a.y);}
    cp operator - (const cp &a){return cp(x-a.x,y-a.y);}
    cp operator * (const cp &a){return cp(x*a.x-y*a.y,x*a.y+y*a.x);}
}A[maxn],B[maxn];
int rev[maxn];
void FFT(cp *f,int n,bool type){
    for(register int i=0;i<n;++i)if(i<rev[i])swap(f[i],f[rev[i]]);
    for(register int p=2;p<=n;p<<=1){
        int len=p>>1;
        cp o(cos(pi/p),type?-sin(pi/p):sin(pi/p));
        for(register int i=0;i<n;i+=p){
            cp gen(1,0);
            for(register int j=i;j<i+len;++j){
                cp cop=f[j+len]*gen;
                f[j+len]=f[j]-cop,f[j]=f[j]+cop,gen=gen*o;
            }
        }
    }
}
int main(){
    int n=read(),m=read();
    for(register int i=0;i<=n;++i)scanf("%lf",&A[i].x);
    for(register int i=0;i<=m;++i)scanf("%lf",&B[i].x);
    m+=n,n=1;
    while(n<=m)n<<=1;
    for(register int i=1;i<n;++i)rev[i]=(rev[i>>1]>>1)|(i&1?n>>1:0);
    FFT(A,n,0),FFT(B,n,0);//dft
    for(register int i=0;i<n;++i)A[i]=A[i]*B[i];
    FFT(A,n,1);//idft
    for(register int i=0;i<=m;++i)printf("%.4lf ",A[i].x);
}
```

## NTT

``` cpp
#define maxn 2100005
const int mod = 998244353;
const int g = 3;
const int ig = 332748118;
int rev[maxn],A[maxn],B[maxn];
inline int quickpow(int x,int y=mod-2){
    int ans=1;
    while(y){
        if(y&1)ans=1ll*ans*x%mod;
        x=1ll*x*x%mod,y>>=1;
    }
    return ans;
}
inline int mix(int x,int y){return x+y>=mod?x+y-mod:x+y;}
void NTT(int *f,int n,bool type){
    for(register int i=0;i<n;++i)if(i<rev[i])swap(f[i],f[rev[i]]);
    for(register int p=2;p<=n;p<<=1){
        int len=p>>1,o=quickpow(type?ig:g,(mod-1)/p);
        for(register int i=0;i<n;i+=p){
            int gen=1,cop;
            for(register int j=i;j<i+len;++j){
                cop=1ll*f[j+len]*gen%mod;
                f[j+len]=mix(f[j],mod-cop),f[j]=mix(f[j],cop),gen=1ll*gen*o%mod;
            }
        }
    }
}
int main(){
    int n=read(),m=read();
    for(register int i=0;i<=n;++i)A[i]=read();
    for(register int i=0;i<=m;++i)B[i]=read();
    m+=n,n=1;
    while(n<=m)n<<=1;
    for(register int i=1;i<n;++i)rev[i]=(rev[i>>1]>>1)|(i&1?n>>1:0);
    NTT(A,n,0),NTT(B,n,0);//dft
    for(register int i=0;i<n;++i)A[i]=1ll*A[i]*B[i]%mod;
    NTT(A,n,1);//idft
    int inv=quickpow(n);
    for(register int i=0;i<=m;++i)printf("%d ",1ll*A[i]*inv%mod);
}
```

---

# 水题

## [力](https://www.luogu.com.cn/problem/P3338)

令$q_0=0$，前半部分就是$\sum\limits_{i=0}^{n-1}\dfrac{q_i}{(n-i)^2}$

设$f(i)=q_i,g(i)=\dfrac{1}{(i+1)^2}$，求的成了$\sum\limits_{i=0}^{n-1}f(i)g(n-i-1)$，$FFT$就没了。

后半部分就是翻过来卷。

## [差分与前缀和](https://www.luogu.com.cn/problem/P5488)

通过手玩会发现$k$阶前缀和答案为$\sum\limits_{j=1}^iC_{i-j+k-1}^{i-j}a_j$，$k$阶差分答案为$\sum\limits_{j=1}^i(-1)^{i-j}C_k^{i-j}a_j$

显然是个卷积的形式。而这个组合数可以递推：

- 对于$C_{i+k-1}^i$，$C_{i+k-1}^0=1,C_{i+k+1}^{i}=C_{i+k}^{i-1}\times \dfrac{k+i-1}{i}$
- 对于$C_k^i$，$C_k^0=1,C_k^i=C_k^{i-1}\times\dfrac{k-i+1}{i}$

这样$k$就能取模了。然后就是$NTT$板子。

## [序列统计](https://www.luogu.com.cn/problem/P3321)

涨姿势了。<span class="spoiler">其实这是一道多项式快速幂</span>

考虑一个朴素的$DP$：设$f(i,j)$为生成了$i$个数乘积为$j$的方案数。任意钦定两个集合的大小拼出来$i$，有$f(i,j)=\sum\limits_{ab\equiv j\pmod m}f(k,a)\times f(i-k,b)(k<i)$。注意只能选一个$k$转移否则就重复了。


用原根转乘法为加法。令$g$为模$m$意义下的原根，$g^A\equiv a,g^B\equiv b\pmod m$。

方程就成了$f(i,j)=\sum\limits_{a+b\equiv j\pmod {\phi(m)}}f(k,a)\times f(i-k,b)(k<i)$

我们发现这其实就是$f$自乘了$n$次，多项式快速幂即可。

没学过多项式快速幂咋办？有另一种理解的方法：

把$n$二进制拆分，最终乘出来的多项式就是$\prod\limits_{i}f(2^i)$。

根据上文能得到$f(i\times 2)=\sum\limits_{ab\equiv j\pmod m}f(i,a)\times f(i,b)$。

和快速幂一样（其实这就是快速幂）从$f(1)$往上自乘就能得到所有的$f(2^i)$。

要注意的是$f$自乘后会得到一个$2(m-1)$项的多项式，因为是在模$m-1$意义下转移的，要把后$m-1$项加到前$m-1$项上。

复杂度$O(m\log^2 n)$。

## [残缺的字符串](https://www.luogu.com.cn/problem/P4173)

$FFT/NTT$经典应用。

先将模式串翻转，后面补通配符。

把通配符看作$0$。考虑两个字符$a,b$相等的条件：
- 两者相等，$a-b=0$
- 其中一个是通配符，$ab=0$

那么以$i$为结尾的子串能匹配的条件就是$\sum\limits_{j=0}^i(a_j-b_{i-j})^2a_jb_{i-j}=0$。带平方是为了防止正负抵消。

拆一下式子就是$\sum\limits_{j=0}^ia_j^3b_{i-j}-2\sum\limits_{j=0}^ia_j^2b_{i-j}^2+\sum\limits_{j=0}^ia_jb_{i-j}^3$

$FFT/NTT$卷三遍即可。

## [礼物](https://www.luogu.com.cn/problem/P3723)

[题解](/2019/12/09/洛谷-P3723-AH2017-HNOI2017-礼物/)

## [万径人踪灭](https://www.luogu.com.cn/problem/P4199)

枚举对称轴，算出有多少对字符关于该轴对称。

因为字符集只有$a,b$，可以用$\sum\limits_{i=0}^j(a_j-a_{i-j})^2$表示不对称的字符对，用总的减去不对称的就是对称的了。

式子拆开，两个平方一个卷积，上$NTT$。

假设算出的字符对有$cnt$个，若该轴为字符，这个字符也可以算进子序列里，有$2^{cnt+1}-1$个子序列关于该轴对称；否则为$2^{cnt}$。

最后第二个条件就减去回文子串数就行。

顺便一提，我又双叒叕$hash$被卡被迫默写$SA$了。{% emoji_coda xiaodianshi/fachou %}

## [DNA](https://www.luogu.com.cn/problem/P3763)

当年用$SAM$写的，但不得不说$FFT/NTT$在字符串匹配上有奇效。

$A,G,C,T$分开算。以$A$为例，$A$看作$1$，不是$A$的看作$0$，贡献为$\sum\limits_{j=0}^i(s_j-s_{i-j})^2$。

四个贡献加起来不超过$3$的就是合法的。

## [染色](https://www.luogu.com.cn/problem/P4491)

[题解](/2019/12/10/洛谷-P4491-HAOI2018-染色)

## [求和](https://www.luogu.com.cn/problem/P4091)

大力推式子：

$\sum\limits_{i=0}^n\sum\limits_{j=0}^i\begin{Bmatrix}i\\j\end{Bmatrix}2^jj!$

$=\sum\limits_{j=0}^n2^jj!\sum\limits_{i=0}^n\begin{Bmatrix}i\\j\end{Bmatrix}$

$=\sum\limits_{j=0}^n2^jj!\sum\limits_{i=0}^n\sum\limits_{k=0}^j\dfrac{(-1)^k}{k!}\dfrac{(j-k)^i}{(j-k)!}$

$=\sum\limits_{j=0}^n2^jj!\sum\limits_{k=0}^j\dfrac{(-1)^k}{k!}\dfrac{\sum\limits_{i=0}^n(j-k)^i}{(j-k)!}$

观察一下$\sum\limits_{i=0}^n(j-k)^i$，这不就一等比数列求和吗。到这里卷积已经很明显了吧。

还有种不用卷积$O(n)$的做法，看不透。