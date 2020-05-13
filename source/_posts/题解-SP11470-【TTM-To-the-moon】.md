---
title: SPOJ11470 TTM - To the moon
date: 2019-02-18 16:59:39
categories: 题解
tags:
	- 数据结构
	- 线段树
	- 可持久化
	- 标记永久化
mathjax: true
keywords: To the moon
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/ttm-tothemoon.jpg
description: 题意概括一下就是区间修改的可持久化线段树。
---

[传送门](https://www.luogu.org/problemnew/show/SP11470)

题意概括一下就是区间修改的可持久化线段树。

<!--more-->

一般的可持久化线段树都是单点改的，这道题肯定不能一个一个改。想想普通线段树的区间修改，是找到最多 $logn$ 个节点并打上懒标记。这样我们当然可以也找到这 $logn$ 个节点并继承。但是标记一下传就会出问题：某两个版本的线段树共用的节点不能修改值。

于是用标记永久化。标记永久化既不需要下传标记，也不需要通过子节点更新自己。

具体来说：**对被修改区间覆盖的节点打上标记，其左右子节点继承上个版本的左右子节点**

放图来看：

![](\images\to the moon-1.png)

就会发现，查询时红色线段树有标记，查询时 $tag$ 会对答案产生影响， $tag$ 不下放就不会影响到两棵线段树的公共节点，就能保证不会互相影响了 $QwQ$ 。

时间复杂度：$O(nlogn)$

空间复杂度：每个区间最多被分成 $2logn$ 个节点，所以为 $O(nlogn)$

细节上注意好时间戳与版本之间的关系（尤其是 $B$ 操作）

上代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 100005
#define inf 0x3f3f3f

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
int root[maxn<<1],wh[maxn];
//wh记录每个时间戳对应哪个版本
int a[maxn];
struct Chairman_Tree{
	long long dat[maxn<<6],tag[maxn<<6];
	int ls[maxn<<6],rs[maxn<<6],cnt;
#define ls(x) ls[x]
#define rs(x) rs[x]
	inline void update(int node){
		dat[node]=dat[ls(node)]+dat[rs(node)];
	}
	void build(int l,int r,int &node){
		node=++cnt;
		if(l==r){
			dat[node]=a[l];
			return;
		}
		int mid=l+r>>1;
		build(l,mid,ls(node));
		build(mid+1,r,rs(node));
		update(node);
	}//初始建树
	void add(int L,int R,int l,int r,int &ne,int ol,int d){
		ne=++cnt;
		dat[ne]=dat[ol]+1ll*(min(R,r)-max(L,l)+1)*d;
		tag[ne]=tag[ol];
		if(L<=l&&R>=r){
			tag[ne]+=(long long)d;
			ls[ne]=ls[ol],rs[ne]=rs[ol];//继承子节点
			return;
		}
		int mid=l+r>>1;
		if(L<=mid)add(L,R,l,mid,ls(ne),ls(ol),d);
		else ls(ne)=ls(ol);//不包含在这个区间内节点，直接继承过来
		if(R>mid)add(L,R,mid+1,r,rs(ne),rs(ol),d);
		else rs(ne)=rs(ol);
	}
	long long ask(int L,int R,int l,int r,int node){
		if(L<=l&&R>=r)return dat[node];
		int mid=l+r>>1;
		long long ans=0;
		if(L<=mid)ans=ask(L,R,l,mid,ls(node));
		if(R>mid)ans+=ask(L,R,mid+1,r,rs(node));
		return ans+1ll*(min(R,r)-max(L,l)+1)*tag[node];
	}
}ct;
int main(){
	int n=read(),m=read(),t=0,now=0;
    //now是当前的时间戳，t是当前的版本
	for(register int i=1;i<=n;++i)
		a[i]=read();
	ct.build(1,n,root[0]);
	while(m--){
		char s[2];
		scanf("%s",s);
		if(s[0]=='C'){
			wh[++now]=++t;
			int l=read(),r=read(),d=read();
			ct.add(l,r,1,n,root[t],root[t-1],d);
		}
		else if(s[0]=='Q'){
			int l=read(),r=read();
			printf("%lld\n",ct.ask(l,r,1,n,root[t]));
		}
		else if(s[0]=='H'){
			int l=read(),r=read(),k=read();
			printf("%lld\n",ct.ask(l,r,1,n,root[wh[k]]));
		}
		else now=read(),root[++t]=root[wh[now]];
	}	
    return 0;
}

```

标记永久化大法好！ $QwQ$
