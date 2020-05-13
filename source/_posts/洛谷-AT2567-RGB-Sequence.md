---
title: 洛谷 AT2567 RGB Sequence
date: 2019-04-12 20:14:52
categories: 题解
mathjax: true
tags:
	- 动态规划
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/rgbsequence.jpg
keywords: RGB Sequence
description: 一道神仙DP题QwQ。继续抄题解
---

[传送门](https://www.luogu.org/problemnew/show/AT2567)

一道神仙$DP$题$QwQ$。~~继续抄题解~~

<!--more-->

设$f(i,j,k)$表示已经处理前$i$个元素、最后一次出现的位置**第二靠前**的颜色位置为$j$、**第三靠前**的位置为$k$时的方案数。显然第一靠前的是$i$，所以不用设出来。

对于每个限制条件，把它限制在右端点。（右端点是临界情况，右端点之前没达到条件可以在后面补足）

用刷表法转移。枚举第$i+1$个元素的颜色并检验每个限制$i+1$的条件是否满足，满足就转移。

具体来说分$3$种情况：

颜色和$i$一样：$f(i+1,j,k)+=f(i,j,k)$

颜色和$j$一样：$f(i+1,i,k)+=f(i,j,k)$

颜色和$k$一样：$f(i+1,i,j)+=f(i,j,k)$

初始状态：$f(1,0,0)=1$

最后答案为$3\*\sum\limits_{i=0}^{n-1}\sum\limits_{j=0}^{\max(i-1,0)}f(n,i,j)$

为啥要乘$3$呢？这里$i,j,k$只是个位置关系，并不是具体某种颜色，所以任意给$i,j,k$选颜色，有$3$种排列。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

#define maxn 305
#define inf 0x3f3f3f3f

const int mod = 1e9+7;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
#define in(l,r,x) (l<=x&&x<=r)
vector<int>limit[maxn];//限制条件
int l[maxn],c[maxn],f[maxn][maxn][maxn];
inline int count(int l,int r,int c1,int c2,int c3){
	return in(l,r,c1)+in(l,r,c2)+in(l,r,c3);
}//计算区间为l,r、颜色位置为c1,c2,c3时，有多少种颜色出现在区间内
int main(){
	int n=read(),m=read(),k1,k2,k3,x,ans=0;
	for(register int i=1;i<=m;++i)
		l[i]=read(),limit[read()].push_back(i),c[i]=read();
	if(n==1){
		ans=3;
		for(register int i=1;i<=m;++i)
			if(c[i]==1)ans=min(ans,1);
			else ans=0;
		printf("%d\n",ans);
		return 0;
	}//要特判n=1，刷表法刷不到1
	f[1][0][0]=1;
	for(register int c1=1;c1<n;++c1)
		for(register int c2=0;c2<c1;++c2)
			for(register int c3=0;c3<=max(c2-1,0);++c3){
				bool ok1=1,ok2=1,ok3=1;//选1、2、3颜色是否可行
				for(register int j=0;j<limit[c1+1].size();++j){
					x=limit[c1+1][j];
					k1=count(l[x],c1+1,c1+1,c2,c3),k2=count(l[x],c1+1,c1,c1+1,c3),k3=count(l[x],c1+1,c1,c2,c1+1);
					ok1&=(k1==c[x]),ok2&=(k2==c[x]),ok3&=(k3==c[x]);
				}//依次检验每个区间
				if(ok1)(f[c1+1][c2][c3]+=f[c1][c2][c3])%=mod;
				if(ok2)(f[c1+1][c1][c3]+=f[c1][c2][c3])%=mod;
				if(ok3)(f[c1+1][c1][c2]+=f[c1][c2][c3])%=mod;
			}	
		for(register int j=0;j<n;++j)
			for(register int k=0;k<=max(j-1,0);++k)
				(ans+=f[n][j][k])%=mod;
	printf("%d\n",((ans<<1)%mod+ans)%mod);//我就不开long long QwQ
}

```
