---
title: Codeforces521D Shop
date: 2019-10-08 07:19:49
categories: 题解
tags:
	- 贪心
keywords: Shop
description: 青岛国庆六日游做的唯一一道题。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/shop.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF521D)

> 快乐是怎么消失的呢，海~~楠~~绵宝宝。。。

<!--more-->

青岛国庆六日游做的唯一一道题。

显然按$1\rightarrow 2\rightarrow 3$的顺序操作是最优的。

答案是乘积最大值，如果只有$3$操作排序一波就好了。

考虑把$1,2$操作转成$3$操作。

赋值同一个位置只取最大的，转成加法。

加法一定是优先取较大的，这样每一个加法操作前的值都是固定的，用操作后的值比上操作前的作为价值转为乘法。

用浮点数大概是精度问题会$WA$，直接存分子分母排序时会爆$long\ long$，需要分子统一减去分母，对答案没有影响。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 200005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
struct operation{
	int type,num;
	long long s,m;
	bool operator < (const operation &x)const{
		if(s*x.m!=m*x.s)return s*x.m>m*x.s;
		return type<x.type;
	}
}fin[maxn];
inline bool cmp(const operation &x,const operation &y){return x.type<y.type;}
int a[maxn],ma[maxn][2];
vector<operation>o[maxn][2];
int main(){
	int n=read(),m=read(),k=read(),x,y,len=0;
	operation p;
	for(register int i=1;i<=n;++i)a[i]=read();
	for(register int i=1;i<=m;++i){
		p.type=read(),x=read(),p.s=read(),p.m=1,p.num=i;
		if(p.type==1){
			if(ma[x][0]<p.s)ma[x][0]=p.s,ma[x][1]=i;
			continue;
		}
		if(p.type==3)--p.s;
		o[x][p.type-2].push_back(p);
	}
	for(register int i=1;i<=n;++i){
		long long sum=a[i];
		if(ma[i][0]>a[i])o[i][0].push_back((operation){1,ma[i][1],ma[i][0]-a[i],1});
		sort(o[i][0].begin(),o[i][0].end());
		for(vector<operation>::iterator iter=o[i][0].begin();iter!=o[i][0].end();++iter){
			fin[++len]=(operation){iter->type,iter->num,iter->s,sum};
			sum+=iter->s;
		}
		for(vector<operation>::iterator it=o[i][1].begin();it!=o[i][1].end();++it)fin[++len]=*it;
	}
	k=min(k,len);
	printf("%d\n",k);
	sort(fin+1,fin+1+len);
	sort(fin+1,fin+1+k,cmp);
	for(register int i=1;i<=k;++i)printf("%d ",fin[i].num);
}

```

