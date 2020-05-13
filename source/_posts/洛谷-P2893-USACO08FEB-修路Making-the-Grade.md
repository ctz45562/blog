---
title: 'USACO-08FEB 修路Making the Grade'
date: 2019-04-11 17:15:20
categories: 题解
tags:
	- 动态规划
	- 潮学
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/xlmakingthegrade.jpg
keywords: 修路Making the Grade
description: 这是一篇研究潮学、用于颓废的题解。
---

[传送门](https://www.luogu.org/problemnew/show/P2893)

~~这是一篇研究潮学、用于颓废的题解。~~

其实这题并不难，但是思维很巧妙。

<!--more-->

一个显而易见的思路就是设$f(i,j)​$为前$i​$个数都满足递增且第$i​$个数为$j​$的最小代价。就有转移方程$f(i,j)=min\{f(i-1,k)+abs(a_i-j) \}(k\le j)​$。不过值域$1e9​$，无论空间还是时间都受不了。

把这个做法写出来并记录方案，~~用瞪眼法~~就会发现修改后的数一定是原数列的某个数。

重新设$f(i,j)​$为前$i​$个数都满足递增且第$i​$个数是原数列中第$j​$大的数的最小代价。先离散化，和原来一样转移，时间复杂度为$O(n^3)​$。然后这玩意可以直接前缀最小值优化成$O(n^2)​$。

写完后一交。。。过了。

~~A了再来证明~~

数（X）学（J）归（B）纳（扯）法：

只有一个数时显然保留原值是最优的。

假设前$k$个值已经满足递增了，考虑第$k+1$个值的处理方案：

这里假设$k=3$：

![](\images\making the grade-1.png)

如上图，潮爷用第一种方案，一定是把自己变得和最强的一样强代价最小；用第二种方案，一定是把比他强的人变得和潮爷一样代价最小。

因为第一个数是原数列的数，所以后面的都还是原数列的数。

证完了！

（$wavwing$说这个结论显而易见，看来是我太蒻了）

（其实这个题还有$O(n\log n)$的做法，也是基于这个结论，~~懒得写了~~）

（顺便说一句洛咕数据未免太水我忘了写递减的情况结果过了？）

~~（所以我并不打算把递减的补上~~

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 2005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int f[maxn][maxn],a[maxn],dis[maxn];
int main(){
	int n=read(),len;
	for(register int i=1;i<=n;++i)
		dis[i]=a[i]=read();
	sort(dis+1,dis+1+n);
	len=unique(dis+1,dis+1+n)-dis-1;//离散化
	memset(f,0x3f,sizeof f);
	for(register int i=1;i<=len;++i)
		f[0][i]=0;
	for(register int i=1;i<=n;++i)
		for(register int j=1;j<=len;++j)
			f[i][j]=min(f[i-1][j]+abs(a[i]-dis[j]),f[i][j-1]);//前缀最小值
	printf("%d\n",f[n][len]);
}

```

