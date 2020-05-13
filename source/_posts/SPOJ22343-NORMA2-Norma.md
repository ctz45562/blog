---
title: SPOJ22343 NORMA2 - Norma
date: 2019-12-29 17:58:23
categories: 题解
tags:
    - cdq分治
keywords: Norma
description:
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.9.1/norma2norma.jpg
mathjax: true
---

[传送门](https://www.luogu.com.cn/problem/SP22343)

> ——别撕时政材料啊，我明年补考还要用的。
> 
> ——明年就有新材料了。

<!--more-->

突然发现自己还不会这种分治算出所有子区间权值的套路，随机钦定了一道来练练。

对于一段中点为$mid$的区间$[l,r]$，它的贡献是经过$mid$的子区间权值加上$[l,mid]$和$[mid+1,r]$的贡献，然后就能分治下去了。

这样只需要考虑怎么算出经过某个点的区间的权值和就可以了。

我的脑回路不太正常，搞了一个无脑暴力的做法：

记$mi_i$为$i$到$mid$所有数的最小值，$ma_i$为最大值，$len_i$为$i$到$mid$的长度。

枚举右端点，考虑左端点的影响。对于$j\in[l,mid],i\in[mid+1,r]$，大力分类讨论$[j,i]$的贡献：

- $case1:mi_j< mi_i\land ma_i>ma_i:mi_jma_j(len_j+len_i)$
- $case2:mi_j<mi_i\land ma_j\le ma_i:mi_jma_i(len_j+len_i)$
- $case3:mi_j\ge mi_i\land ma_j>ma_i:mi_ima_j(len_j+len_i)$
- $case4:mi_j\ge mi_i\land ma_j\le ma_i:mi_ima_i(len_j+len_i)$

显然$ma$和$mi$是单调的，而且一个左端点的变化情况只有可能是$case1\rightarrow case2 \rightarrow case4$或$case1\rightarrow case3\rightarrow case4$。所以可以用一个指针维护左端点情况，在随着右端点移动向左跑；再对$case2,3$开两个队列判断是否可以进入$case4$，对每种$case$开两个变量随便搞一搞就行了。复杂度还是$O(n\log n)$。

~~最棒的是这个方法无论是代码难度、美观度还是常数都被正解踩爆了呢~~

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <queue>

#define maxn 500005
#define inf 0x3f3f3f3f

const int mod = 1e9;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int a[maxn],mi[maxn],ma[maxn],mul[maxn],ans;
queue<int>min_q,max_q;
#define clear(q) while(!q.empty())q.pop()
inline int mix(int x,int y){return x+y>=mod?x+y-mod:x+y;}
void solve(int l,int r){
	if(l==r){ans=mix(ans,1ll*a[l]*a[l]%mod);return;}
	clear(min_q);
	clear(max_q);
	int mid=l+r>>1,j=mid,min_max_sum,min_max_cnt,min_sum=0,min_cnt=0,max_sum=0,max_cnt=0,sum=0,cnt=0,Mi=inf,Ma=0;
	mi[mid]=ma[mid]=a[mid],min_max_sum=min_max_cnt=mul[mid]=1ll*a[mid]*a[mid]%mod;
	for(register int i=mid-1;i>=l;--i)mi[i]=min(mi[i+1],a[i]),ma[i]=max(ma[i+1],a[i]),mul[i]=1ll*mi[i]*ma[i]%mod,min_max_sum=mix(min_max_sum,1ll*mul[i]*(mid-i+1)%mod),min_max_cnt=mix(min_max_cnt,mul[i]);
	for(register int i=mid+1;i<=r;++i){
		Mi=min(Mi,a[i]),Ma=max(Ma,a[i]);
		while(j>=l){
			if(mi[j]<Mi&&ma[j]>Ma)break;
			min_max_sum=mix(min_max_sum,mod-1ll*mul[j]*(mid-j+1)%mod);
			min_max_cnt=mix(min_max_cnt,mod-mul[j]);
			if(mi[j]>=Mi&&ma[j]<=Ma)sum=mix(sum,mid-j+1),++cnt;
			else if(mi[j]>=Mi)max_sum=mix(max_sum,1ll*ma[j]*(mid-j+1)%mod),max_cnt=mix(max_cnt,ma[j]),max_q.push(j);
			else min_sum=mix(min_sum,1ll*mi[j]*(mid-j+1)%mod),min_cnt=mix(min_cnt,mi[j]),min_q.push(j);
			--j;
		}
		while(!min_q.empty()){
			int k=min_q.front();
			if(mi[k]<Mi)break;
			min_sum=mix(min_sum,mod-1ll*mi[k]*(mid-k+1)%mod),min_cnt=mix(min_cnt,mod-mi[k]);
			sum=mix(sum,mid-k+1),++cnt;
			min_q.pop();
		}
		while(!max_q.empty()){
			int k=max_q.front();
			if(ma[k]>Ma)break;
			max_sum=mix(max_sum,mod-1ll*ma[k]*(mid-k+1)%mod),max_cnt=mix(max_cnt,mod-ma[k]);
			sum=mix(sum,mid-k+1),++cnt;
			max_q.pop();
		}
		ans=mix(ans,mix(mix(mix(min_max_sum,1ll*min_max_cnt*(i-mid)%mod),mix(1ll*max_sum*Mi%mod,1ll*max_cnt*Mi%mod*(i-mid)%mod)),mix(mix(1ll*min_sum*Ma%mod,1ll*min_cnt*Ma%mod*(i-mid)%mod),mix(1ll*sum*Mi%mod*Ma%mod,1ll*cnt*Mi%mod*Ma%mod*(i-mid)%mod))));
	}
	solve(l,mid),solve(mid+1,r);
}
int main(){
	int n=read();
	for(register int i=1;i<=n;++i)a[i]=read();
	solve(1,n);
	printf("%d\n",ans);
}

```