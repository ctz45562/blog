---
title: Codeforces875E Delivery Club
date: 2019-11-09 15:48:37
categories: 题解
tags:
    - 二分
    - 动态规划
    - 贪心
keywords: Delivery Club
description: 第一眼二分，第二眼就告辞了。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/deliveryclub.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF875E)

第一眼二分，第二眼就告辞了。

其实不算是动态规划，应该是递推的。只是我懒得再开新标签了。

<!--more-->

正着推好像只能$O(n^2)$的$DP$。考虑倒着做。

假设当前二分值为$mid$。

维护一个限制区间$L$，表示要满足后面已考虑的点都不超出$mid$的限制，当前点能选取的区间。

一开始有一个快递员在$x_n$上，$L=[x_n-mid,x_n+mid]$，考虑钦定谁去点$x_{n-1}$。

- 若$x_{n-1}\in L$，可以钦定其中一个快递员到$x_{n-1}$，另一个快递员到$x_n$（不用考虑在$x_{n-1}$前是否可行，这是之后要考虑的）。此时$x_n$也不受$mid$的限制了，$L=[x_{n-1}-mid,x_{n-1}+mid]$。
- 否则，必须钦定位于$x_n$的快递员去送$x_{n-1}$（若另一个快递员送的话$x_{n-1}$与$x_n$的距离会超过限制）。此时$x_n$和$x_{n-1}$的限制都需要考虑，$L=L\bigcap[x_{n-1}-mid,x_{n-1}+mid]$。

这样的关系同样满足于前面所有$x_i$。递推到$x_1$时，只要判断$s_1,s_2$是否有一个属于$L$即可。递推过程中出现空集也不可行。不过若出现过空集，推到最后$L$也一定是空集，所以不用特判了。

代码：
``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 100005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int x[maxn],n,s1,s2;
#define in(x,l,r) (x>=l&&x<=r)
inline bool check(int len){
	int l=x[n]-len,r=x[n]+len;
	for(register int i=n-1;i;--i){
		if(in(x[i],l,r))l=x[i]-len,r=x[i]+len;
		else l=max(l,x[i]-len),r=min(r,x[i]+len);
	}
	return in(s1,l,r)||in(s2,l,r);
}
int main(){
	n=read(),s1=read(),s2=read();
	int l=max(s1,s2)-min(s1,s2),r=l,mid;
	for(register int i=1;i<=n;++i)r=max(r,x[i]=read());
	while(l<r){
		mid=l+r>>1;
		if(check(mid))r=mid;
		else l=mid+1;
	}
	printf("%d\n",l);
}
```