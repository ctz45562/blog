---
title: '洛谷 P2303 [SDOi2012]Longge的问题'
date: 2019-03-26 19:11:50
categories: 题解
tags:
	- 数论
	- 欧拉函数
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/longgedwt.jpg
keywords: Longge的问题
description: 写篇题解纪念一下我想了一天的沙雕题。
---

[传送门](https://www.luogu.org/problemnew/show/P2303)

写篇题解纪念一下我想了一天的~~沙雕~~题。

<!--more-->

显然，$gcd(i,n)$一定等于$n$的某个因子。

$O(\sqrt n)$筛出$n$的因子，假设有$cnt$个，分别为$a_1,a_2...a_{cnt}$。

对于任意一个因子$a_i$，若有$gcd(ka_i,n)=a_i$，则$gcd(k,n/a_i)=1$。

即$k$与$n/a_i$互质。所以$a_i$的贡献为$\varphi(n/a_i)\*a_i$。

答案即为$\sum\limits_{i=1}^{cnt}\varphi(n/a_i)\*a_i$

~~嘤嘤嘤我居然想了一天？~~

复杂度：$O(cnt\sqrt n)$

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

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
long long phi(long long x){
	long long ans=x;
	for(long long i=2;i*i<=x;++i){
		if(x%i==0){
			ans=ans/i*(i-1);
			while(x%i==0)x/=i;
		}
	}
	if(x>1)ans=ans/x*(x-1);
	return ans;
}
void Get_Factor(){
	long long ans=0;
	for(long long i=1;i*i<=n;++i)
		if(n%i==0){
			ans+=phi(n/i)*i;
			if(i*i!=n)ans+=phi(i)*n/i;
		}
	printf("%lld\n",ans);
}
int main(){
	n=read<long long>();
	Get_Factor();
}

```

