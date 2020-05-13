---
title: '洛谷 P3558 [POI2013]BAJ-Bytecomputer'
date: 2019-04-13 08:34:17
categories: 题解
tags:
	- 动态规划
mathjax: true
description: 其实没必要分类讨论。
keywords: BAJ-Bytecomputer
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/bajbytecomputer.jpg
---

[传送门](https://www.luogu.org/problemnew/show/P3558)

其实没必要分类讨论。

<!--more-->

很容易想到设$f(i,j)$表示前$i$个数满足单调不降、第$i$个数为$j$的最少操作次数。

再定义一个函数$calc(x,y,state)$表示：把$a[i-1]$可以取到的值压进$state$里（二进制下每一位依次表示能取到$-1,0,1$），$a[i]$从$x$修改为$y$的最小代价。

预处理$calc$，转移方程就能直接写成：

$f(i,j)=\min\{f(i-1,k)+calc(a[i],j,state)\}(k\le j)$（把$\min(a[i-1],k)$到$\max(a[i-1],k)$每个值加入$state$）

解释一下：枚举$a[i-1]$修改为了$k$，那么在$a[i-1]$修改的过程中会取到$\min(a[i-1],k)$到$\max(a[i-1],k)$的每个值，想用哪个值，就在它修改到这个值的时候去更新$a[i]$。

然后就是预处理$calc$了：

- 若$x=y$，$calc(x,y,state)=0$
- 若$x< y$，只要$state$能取到$1$，就有$calc(x,y,state)=y-x$
- 若$x>y$，只要$state$能取到$-1$，就有$calc(x,y,state)=x-y$
- 其他情况均为$inf$

初始状态$f(1,a[1])=0$，答案为$\min\{f(n,-1),f(n,0),f(n,1)\}$，复杂度$O(9n)$。

题面没有给范围，大概是$1e6$，无解还要输出"$BRAK$"

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 1000005

const long long inf = 4557430888798830399ll;

using namespace std;

inline int read(){
    int x=0,y=0;
    char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
    while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
    return y?-x:x;
}
const int S = 7;
int a[maxn]={1};
long long f[maxn][3],s[3][3][S+2];
//s是calc函数的值
void init(){
    memset(f,0x3f,sizeof f);
    memset(s,0x3f,sizeof s);
    for(register int s0=0;s0<=S;++s0)
        for(register int i=0;i<3;++i)
            s[i][i][s0]=0;//x=y
    for(register int s0=0;s0<=S;++s0)
        if(s0&1)s[2][1][s0]=s[1][0][s0]=1,s[2][0][s0]=2;//state能取到-1
    for(register int s0=0;s0<=S;++s0)
        if(s0&4)s[0][1][s0]=s[1][2][s0]=1,s[0][2][s0]=2;//state能取到1
    //预处理s
}
int main(){
    int n=read(),state;
    long long ans=inf;
    for(register int i=1;i<=n;++i)
        a[i]=read()+1;
    //防止负数下标全部+1
    init();
    f[1][a[1]]=0;
    for(register int i=2;i<=n;++i)
        for(register int j=0;j<3;++j)
            for(register int k=0;k<=j;++k){
                state=0;
                for(register int p=min(a[i-1],k);p<=max(a[i-1],k);++p)
                    state|=1<<p;
                f[i][j]=min(f[i][j],f[i-1][k]+s[a[i]][j][state]);
            }
    ans=min(f[n][0],min(f[n][1],f[n][2]));
    if(ans==inf)puts("BRAK");
    else printf("%lld\n",ans);
}

```

