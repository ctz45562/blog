---
title: 'USACO-19JAN Redistricting'
date: 2019-02-25 12:37:51
categories: 题解
tags:
	- 动态规划
	- 平衡树
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/redistricting.jpg
keywords: Redistricting
description: 正解好像是O(n)的单调队列优化贪心？这里提供一种好想点的O(nlogn)思路。
---

[传送门](https://www.luogu.org/problemnew/show/P5202)

正解好像是$O(n)$的单调队列优化贪心？这里提供一种好想点的$O(nlogn)$思路。

<!--more-->

首先把 $H$ 看作 $1$ ， $G$ 看作 $-1$ ，处理一下前缀和 $sum$ ，这样就能快速判断一段区间两种牛数量关系了。

用 $f(i)$ 表示把前 $i$ 块牧草地分完最少能有几段更赛牛区域。转移时枚举一下最后一段区域的长度，就有：

$f(i)=min\{f(i-j)+[sum[i]-sum[i-j]\le 0]\}(1\le j\le k)$

初值$f[0]=0$，答案为$f[n]$。

$[sum[i]-sum[i-j]\le 0]$意为区域$[i-j+1,i]$是否为更赛牛区域。

时间复杂度：$O(nk)$，显然过不了。

把$sum[i]-sum[i-j]\le 0$移项得$sum[i]\le sum[i-j]$

这样就可以造一棵存两个值$sum[i]$和$f[i]$的平衡树，以$sum[i]$排序，每个节点取一下子树中的$min(f[i])$。查询时遍历一遍平衡树，大于等于当前$sum[i]$的要$+1$，取一下$min$，就能用$logn$的时间更新，然后再插入。同时为了保证取到前面的$k$个值要及时删除$QwQ$

时间复杂度：$O(nlogn)$

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 300005
#define inf 0x3f3f3f3f
#define pn putchar('\n')
#define px(x) putchar(x)
#define ps putchar(' ')
#define pd puts("======================")
#define pj puts("++++++++++++++++++++++")

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
}//快读
inline int ran(){
	static int seed=45562;
	return seed=(seed*48271LL%2147483647);
}//rand函数，好像能优化一下常数？
struct Treap{
	int dat[maxn],num[maxn],ls[maxn],rs[maxn],ra[maxn],mi[maxn],cnt,root;
    //dat为用于比较的关键值（sum[i])，num为存起来的f[i]，mi为子树最小f[i]
#define ls(x) ls[x]
#define rs(x) rs[x]
	inline void update(int node){
		mi[node]=min(min(mi[ls(node)],mi[rs(node)]),num[node]);
	}
	void right(int &node){
		int rec=ls(node);
		ls(node)=rs(rec);
		rs(rec)=node;
		node=rec;
		update(rs(node));
		update(node);
	}
	void left(int &node){
		int rec=rs(node);
		rs(node)=ls(rec);
		ls(rec)=node;
		node=rec;
		update(ls(node));
		update(node);
	}
	void insert(int &node,int d1,int d2){
		if(!node){
			node=++cnt;
			dat[node]=d1;
			mi[node]=num[node]=d2;
			ra[node]=ran();
			return;
		}
		if(dat[node]<d1||(dat[node]==d1&&num[node]<d2)){
			insert(rs(node),d1,d2);
			if(ra[rs(node)]>ra[node])left(node);
		}
		else {
			insert(ls(node),d1,d2);
			if(ra[ls(node)]>ra[node])right(node);
		}
		update(node);
	}
	void del(int &node,int d1,int d2){
		if(dat[node]==d1&&num[node]==d2){
			if(ls(node)&&rs(node)){
				if(ra[ls(node)]>ra[rs(node)])right(node),del(rs(node),d1,d2);
				else left(node),del(ls(node),d1,d2);
				update(node);
			}
			else node=ls(node)+rs(node);
			return;
		}
		if(dat[node]<d1||(dat[node]==d1&&num[node]<d2))del(rs(node),d1,d2);
		else del(ls(node),d1,d2);
		update(node);
	}
	int Get(int d){
		int node=root,ans=inf;
		while(node){
			if(d<=dat[node])ans=min(ans,min(num[node],mi[rs(node)])+1),node=ls(node);
            //当前节点大于等于待查询值，则它的右子树都大于等于，答案+1
			else ans=min(ans,min(num[node],mi[ls(node)])),node=rs(node);
            //否则左子树都比它小，不+1
		}
		return ans;
	}//查询函数
	Treap(){
		num[0]=mi[0]=inf;
	}
}tr;
int f[maxn],sum[maxn],n,k;
char a[maxn];
int main(){
	n=read(),k=read();
	scanf("%s",a+1);
	for(register int i=1;i<=n;++i)
		if(a[i]=='H')sum[i]=sum[i-1]+1;
		else sum[i]=sum[i-1]-1;
	tr.insert(tr.root,0,0);//插入初值
	for(register int i=1;i<=n;++i){
		if(i>k)tr.del(tr.root,sum[i-k-1],f[i-k-1]);//删除不在当前区间的值
		f[i]=tr.Get(sum[i]);
		tr.insert(tr.root,sum[i],f[i]);
	}
	printf("%d\n",f[n]);
}
```



