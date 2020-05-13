---
title: SPOJ6779 GSS7 - Can you answer these queries VII
date: 2019-02-17 10:45:26
categories: 题解
tags:
	- 数据结构
	- LCT
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/canyouanswerthesequeriesVII.jpg
keywords: GSS7
description: 题解都是树剖，没有LCT？
---

[传送门](https://www.luogu.org/problemnew/show/SP6779)

题解都是树剖，没有LCT？

<!--more-->

裸的树上链查询最大子段和+链赋值。一开始想的树剖，不过树剖查询时会在两个点之间来回跳，两边信息的合并是有一定思维量的。~~因为懒得想~~就写的LCT。（毕竟LCT能直接把整个链完整的提取出来真的方便啊）

最大子段和这一系列题都是，维护一下以这个区间总和sum，覆盖左端点的最大和ll，覆盖右端点的最大和rr，最大子段和ma。

$\text{ll=max(左儿子ll，左儿子sum+自己的点权+右儿子ll)}$

$\text{rr=max(右儿子rr，右儿子sum+自己的点权+左儿子rr)}$

$\text{ma=max(左儿子ma，右儿子ma，左儿子rr+自己的点权+右儿子ll)}$

链赋值，若赋值为正，ll、rr、ma就改为sum（所有点都选）；为负，则改为0（可以不选）

代码：
```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <map>

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
}//快读
int n;
struct Link_Cut_Tree{
	int ll[maxn],rr[maxn],ma[maxn],sum[maxn],siz[maxn],son[maxn][2],fa[maxn],rev[maxn],tag[maxn],st[maxn],dat[maxn];
#define son(x,y) son[x][y]
#define whson(x) (son[fa[x]][1]==x)
#define root(x) (son[fa[x]][0]!=x&&son[fa[x]][1]!=x)
	inline void update(int node){
		siz[node]=siz[son(node,0)]+siz[son(node,1)]+1;//维护siz用于更新sum
		ll[node]=max(ll[son(node,0)],sum[son(node,0)]+dat[node]+ll[son(node,1)]);
		rr[node]=max(rr[son(node,1)],sum[son(node,1)]+dat[node]+rr[son(node,0)]);
		ma[node]=max(max(ma[son(node,0)],ma[son(node,1)]),ll[son(node,1)]+dat[node]+rr[son(node,0)]);
		sum[node]=sum[son(node,0)]+sum[son(node,1)]+dat[node];
	}	
	inline void addedge(int s,int f,int wh){
		fa[s]=f,son(f,wh)=s;
	}
	inline void datadown(int node,int d){
		sum[node]=siz[node]*d;
		if(d>0)ll[node]=rr[node]=ma[node]=sum[node];
		else ll[node]=rr[node]=ma[node]=0;
		dat[node]=d,tag[node]=1;
	}
	inline void reverdown(int node){
		rev[node]^=1;
		swap(son(node,0),son(node,1));
		swap(ll[node],rr[node]);//ll、rr也要交换！
	}
	inline void pushdown(int node){
		if(rev[node]){
			if(son(node,0))reverdown(son(node,0));
			if(son(node,1))reverdown(son(node,1));
			rev[node]=0;
		}
		if(tag[node]){
			if(son(node,0))datadown(son(node,0),dat[node]);
			if(son(node,1))datadown(son(node,1),dat[node]);
			tag[node]=0;
		}
	}
	inline void zhuan(int x){
		int f=fa[x],gf=fa[f],wh=whson(x);
		fa[x]=gf;
		if(!root(f))son(gf,whson(f))=x;
		addedge(son(x,wh^1),f,wh);
		addedge(f,x,wh^1);
		update(f),update(x);
	}
	void splay(int x){
		int top=1,y=x;
		st[1]=x;
		while(!root(y))st[++top]=y=fa[y];
		while(top)pushdown(st[top--]);
		while(!root(x)){
			if(!root(fa[x]))
				zhuan(whson(x)^whson(fa[x])?fa[x]:x);
			zhuan(x);
		}
	}
	void access(int x){
		for(int y=0;x;y=x,x=fa[x])
			splay(x),son(x,1)=y,update(x);
	}
	void makeroot(int x){
		access(x),splay(x),reverdown(x);
	}
	void link(int x,int y){
		makeroot(x),access(y),splay(y);
		fa[x]=y;
	}
	void change(int x,int y,int d){
		makeroot(x),access(y),splay(y),datadown(y,d);
	}
	void Get(int x,int y){
		makeroot(x),access(y),splay(y);
		printf("%d\n",ma[y]);
	}
	void init(){
		for(register int i=1;i<=n;++i){
			dat[i]=sum[i]=read();
			if(dat[i]>0)ll[i]=rr[i]=ma[i]=dat[i];
			siz[i]=1;//初始化
		}
	} 
}lct;
int main(){
	n=read();	
	lct.init();
	for(register int i=1;i<n;++i){
		int a=read(),b=read();
		lct.link(a,b);
	}
	int m=read();
	while(m--){
		int s=read(),x=read(),y=read();
		if(s==1)lct.Get(x,y);
		else {
			int d=read();
			lct.change(x,y,d);
		}
	}
}

```

这种讲究查询顺序、信息合并的题（我知道的还有[SDOI2011染色](https://www.luogu.org/problemnew/show/P2486)），用于树剖练手、锻炼思维很好。不过比赛遇到这种题还是写好想又好写的LCT吧。（毕竟细节写错一点就炸了）
