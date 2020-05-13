---
title: Codeforces613D Kingdom and its Cities
date: 2019-08-04 20:48:24
categories: 题解
tags:
	- 动态规划
	- 虚树
keywords: Kingdom and its Cities
description: 一个思路清奇代码复杂常数大看起来很假的DP做法。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/kingdomanditscities.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF613D)

一个思路清奇代码复杂常数大看起来很假的$DP$做法。

<!--more-->

建出虚树，设$f(i,0)$为使点$i$的子树内的关键点互不联通，且存在关键点与点$i$联通的最小代价；$f(i,1)$为使点$i$的子树内的关键点互不联通，且不存在关键点与点$i$联通的最小代价。

**若$i$为关键点：**

$f(i,1)$的情况不可能成立（关键点$i$和$i$联通），$f(i,1)=inf$。

判断一下是否有$i$的儿子为关键点且与$i$在原树上有边，存在则无解。

对于$f(i,0)$，处于情况$0$的儿子为满足**关键点互不联通**，需断掉路径上的某个点，处于情况$1$的儿子不用管，$f(i,0)=\sum\limits_{j\in son(i)}\min\{f(j,1),f(j,0)+1\}$

**若$i$不为关键点：**

若$i$有不少于$2$个点为关键点，不可能存在情况$0$，此时$f(i,0)=inf$。

否则其儿子里需有且只有处于情况$0$，即$f(i,0)=\sum\limits_{j\in son(i)}f(j,1)+\min\limits_{j\in son(i)}\{f(j,0)-f(j,1)\}$

对于$f(i,1)$，要么断开点$i$，儿子随便选；要么儿子都处于情况$1$。即$f(i,1)=\min\{\left(\sum\limits_{j\in son(i)}\min\{f(j,0),f(j,1)\}\right)+1,\sum\limits_{j\in son(i)}f(j,1)\}$

 

最后答案为$\min\{f(1,0),f(1,1)\}$。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

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
namespace origin{
	int seg[maxn],top[maxn],son[maxn],fa[maxn],deep[maxn],siz[maxn],h[maxn],all,num;
	struct edge{
		int pre,to;
	}e[maxn<<1];
	inline void add(int from,int to){
		e[++num].pre=h[from],h[from]=num,e[num].to=to;
	}
	void dfs1(int node=1){
		siz[node]=1;
		int x;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			if(!siz[x]){
				fa[x]=node,deep[x]=deep[node]+1,dfs1(x),siz[node]+=siz[x];
				if(siz[x]>siz[son[node]])son[node]=x;
			}
		}
	}
	void dfs2(int node=1){
		seg[node]=++all;
		if(son[node]){
			top[son[node]]=top[node],dfs2(son[node]);
			int x;
			for(register int i=h[node];i;i=e[i].pre){
				x=e[i].to;
				if(!seg[x])top[x]=x,dfs2(x);
			}
		}
	}
	int lca(int x,int y){
		while(top[x]!=top[y])deep[top[x]]<deep[top[y]]?y=fa[top[y]]:x=fa[top[x]];
		return deep[x]<deep[y]?x:y;
	}
}
namespace virt{
	int h[maxn],f[maxn][2],num,n;
	bool vis[maxn],flag;
	vector<int>p;
	struct edge{
		int pre,to;
		bool l;
	}e[maxn<<1];
	inline void add(int from,int to){
		bool l=(origin::fa[from]==to)||(origin::fa[to]==from);
		e[++num].pre=h[from],h[from]=num,e[num].to=to,e[num].l=l;
		e[++num].pre=h[to],h[to]=num,e[num].to=from,e[num].l=l;
	}	
	inline bool cmp(int x,int y){return origin::seg[x]<origin::seg[y];}
	struct Monostack{
		int sta[maxn],top;
		void push(int x){sta[++top]=x;}
		void clear(){
			for(register int i=2;i<=top;++i)add(sta[i],sta[i-1]);
			top=0;
		}
		void check(int x){
			if(x==1)return;
			int l=origin::lca(x,sta[top]);
			h[x]=0;
			if(l!=sta[top]){
				while(origin::seg[l]<origin::seg[sta[top-1]])add(sta[top],sta[top-1]),--top;
				--top;
				if(l!=sta[top])h[l]=0,add(sta[top+1],l),push(l);
				else add(sta[top+1],l);
			}
			push(x);
		}
	}s;
	void dfs(int node=1,int fa=0){
		f[node][0]=(vis[node]^1)*n,f[node][1]=vis[node]*n;
		int x,siz=0,rec=0,ma=0;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			if(x==fa)continue;
			siz+=vis[x],dfs(x,node);
			if(vis[node]){
				if(vis[x]&&e[i].l)flag=1;	
				f[node][0]+=min(f[x][1],f[x][0]+1);
			}
			else {
				ma=max(ma,f[x][1]-f[x][0]);
				rec+=min(f[x][0],f[x][1]);
				f[node][1]+=f[x][1];
			}
		}
		if(!vis[node]){
			if(siz<=1)f[node][0]=f[node][1]-ma;
			f[node][1]=min(f[node][1],rec+1);
		}
	}
	void build(){
		h[1]=0,s.push(1);
		for(vector<int>::iterator iter=p.begin();iter!=p.end();++iter)s.check(*iter);
		s.clear();
	}
	void solve(){
		p.clear(),flag=0,n=read(),num=0;
		int x;
		for(register int i=1;i<=n;++i)p.push_back(x=read()),vis[x]=1;
		sort(p.begin(),p.end(),cmp);
		build(),dfs();
		printf("%d\n",flag?-1:min(f[1][0],f[1][1]));
		for(vector<int>::iterator iter=p.begin();iter!=p.end();++iter)vis[*iter]=0;
	}
}
int main(){
	using namespace origin;
	int n=read(),x,y;
	for(register int i=1;i<n;++i)x=read(),y=read(),add(x,y),add(y,x);
	dfs1(),dfs2(),n=read();
	while(n--)virt::solve();
}
```

