---
title: '洛谷 P4556 [Vani有约会]雨天的尾巴'
date: 2019-03-03 15:32:34
categories: 题解
tags:
	- 数据结构
	- 树链剖分
	- 线段树
mathjax: true
keywords: 雨天的尾巴
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/ytdwb.jpg
description: 题解没有树剖转化序列做的？来水一发题解。
---
[传送门](https://www.luogu.org/problemnew/show/P4556)

题解没有树剖转化序列做的？来水一发题解。

<!--more-->

首先简化一下，如果把问题改成序列上做，即：

> 每个操作在一段区间上加一种颜色，求每个点数量最多的颜色。

用一下差分的思想：记操作区间为$[L,R]$，扫描整个序列。如果**扫到**某个操作的$L$,就把它的颜色$+1$；如果**扫过**某个操作的$R$就把它的颜色$-1$。每个点的答案就是扫到这个点时颜色数量最多的一个。这些可以用线段树来维护。

现在放到树上了，为链操作。就能使用树剖。树剖珂以把一条链剖成$logn$个**编号连续**的链。这样就能把每个操作剖成$logn$个新操作，用上面的方法放到序列上做就行了$QwQ$。

时间复杂度：每个操作会被分成$logn$个新操作，每个新操作会执行一次$O(logn)$的线段树操作。总复杂度$O(nlog^2n)$

空间复杂度：$O(nlogn)$

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

#define maxn 100005
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
}
int h[maxn],num;
struct edge{
	int pre,to;
}e[maxn<<1];
struct Question{
	int d,s;
};
vector<Question>q[maxn];
//Question用来存操作
int seg[maxn],pos[maxn],son[maxn],siz[maxn],deep[maxn],fa[maxn],top[maxn],cnt[maxn],ans[maxn],all;
inline void add(int from,int to){
	e[++num].pre=h[from],h[from]=num,e[num].to=to;
}
struct Segment_Tree{
	int dat[maxn<<2],wh[maxn<<2];
#define ls(x) (x<<1)
#define rs(x) (x<<1|1)
	inline void update(int node){
		if(!dat[ls(node)]&&!dat[rs(node)])dat[node]=wh[node]=0;
		else if(dat[ls(node)]>dat[rs(node)]||(dat[ls(node)]==dat[rs(node)]&&wh[ls(node)]<wh[rs(node)]))dat[node]=dat[ls(node)],wh[node]=wh[ls(node)];
		else dat[node]=dat[rs(node)],wh[node]=wh[rs(node)];
	}
    //wh表示最大值的编号
	void build(int l,int r,int node){
		if(l==r){
			wh[node]=l;
			return;
		}
		int mid=l+r>>1;
		build(l,mid,ls(node));
		build(mid+1,r,rs(node));
	}
	void add(int poi,int l,int r,int node,int d){
		if(l==r){
			dat[node]+=d;
			return;
		}
		int mid=l+r>>1;
		if(poi<=mid)add(poi,l,mid,ls(node),d);
		else add(poi,mid+1,r,rs(node),d);
		update(node);
	}
}st;
struct Tree_Chain_split{
	void dfs1(int node=1){
		siz[node]=1;
		for(register int i=h[node];i;i=e[i].pre){
			int x=e[i].to;
			if(!siz[x]){
				fa[x]=node,deep[x]=deep[node]+1;
				dfs1(x);
				if(siz[x]>siz[son[node]])son[node]=x;
				siz[node]+=siz[x];
			}
		}	
	}
	void dfs2(int node=1){
		seg[node]=++all;
		pos[all]=node;	
		if(son[node]){
			top[son[node]]=top[node];
			dfs2(son[node]);
			for(register int i=h[node];i;i=e[i].pre){
				int x=e[i].to;
				if(!seg[x])top[x]=x,dfs2(x);
			}
		}
	}
	void ask(int x,int y,int d){
		Question add,minus;
		add.s=minus.s=d,add.d=1,minus.d=-1;
		while(top[x]!=top[y]){
			if(deep[top[x]]<deep[top[y]])swap(x,y);
			q[seg[top[x]]].push_back(add),q[seg[x]+1].push_back(minus);
            //注意减去的要加1
			x=fa[top[x]];
		}
		if(deep[x]<deep[y])swap(x,y);
		q[seg[y]].push_back(add),q[seg[x]+1].push_back(minus);
	}//拆分操作函数
}tcs;
int main(){
	int n=read(),m=read(),tp=0;
	for(register int i=1;i<n;++i){
		int a=read(),b=read();
		add(a,b),add(b,a);
	}
	tcs.dfs1(),tcs.dfs2();
	while(m--){
		int a=read(),b=read(),d=read();
		tp=max(tp,d);
		tcs.ask(a,b,d);
	}
	st.build(1,tp,1);
	for(register int i=1;i<=n;++i){
		for(register int j=0;j<q[i].size();++j)
			st.add(q[i][j].s,1,tp,1,q[i][j].d);
		ans[pos[i]]=st.wh[1];
	}
	for(register int i=1;i<=n;++i)
		printf("%d\n",ans[i]);
}
```
