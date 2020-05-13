---
title: Codeforces715C Digit Tree
date: 2019-09-23 21:14:44
categories: 题解
tags:
	- 点分治
keywords: Digit Tree
description: 快CSP了所以着重练习省选算法（？）
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/digittree.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF715C)

快$CSP$了所以着重练习省选算法（？）

<!--more-->

> 统计有序点对$\langle x,y\rangle$个数，满足$x$到$y$路径连成的数字能被$m(\gcd(m,10)=1)$整除。

路径统计显然是点分了。

记$root$为分治中心，$dep_i$为点$i$到$root$的经过的边数，$a_{\langle i,j\rangle}$为点$i$到点$j$路径形成的数字模$m$的值。

则有向路径$\langle i,j\rangle$有贡献的条件为$a_{\langle i,root\rangle}+a_{\langle root,j\rangle}\times10^{dep_i}\equiv0\pmod m$。

$\gcd(m,10)=1$，直接把$10^{dep_i}$除掉：

$a_{\langle i,root\rangle}\times10^{-dep_i}+a_{\langle root,j\rangle}\equiv0\pmod m$

处理波$10^a$的逆元开个`map`存$a_{\langle i,root\rangle}\times 10^{-dep_i}$就行了。

不知道为啥`unordered_map`$T$了。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <map>

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
int siz[maxn],h[maxn],inv[maxn],pw[maxn]={1},num,all,root,mx,mod;
bool vis[maxn];
long long ans;
map<int,int>tax;
struct edge{
	int pre,to,l;
}e[maxn<<1];
inline void add(int from,int to,int l){
	e[++num]=(edge){h[from],to,l};
	h[from]=num;
}
void exgcd(int a,int b,int &x,int &y){
	if(!b){x=1,y=0;return;}
	exgcd(b,a%b,y,x),y-=a/b*x;
}
inline int INV(int a){
	int x,y;
	exgcd(a,mod,x,y);
	return (x%mod+mod)%mod;
}
void getroot(int node,int f=0){
	int ma=all-siz[node];
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==f||vis[x])continue;
		getroot(x,node),ma=max(ma,siz[x]);
	}
	if(ma<mx)mx=ma,root=node;
}
void Count(int node,int d,int dep,int sum,int f=0){
	tax[1ll*sum*inv[dep]%mod]+=d;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==f||vis[x])continue;
		Count(x,d,dep+1,(10ll*sum%mod+e[i].l)%mod,node);
	}
}
void calc(int node,int sum,int dep,int f=0){
	siz[node]=1,ans+=tax[(mod+mod-sum)%mod];
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==f||vis[x])continue;
		calc(x,(sum+1ll*pw[dep]*e[i].l%mod)%mod,dep+1,node);
		siz[node]+=siz[x];
	}
}
void solve(int node){
	vis[node]=1,tax.clear();
	int x;
	Count(node,1,0,0);
	ans+=tax[0]-1;
	for(register int i=h[node];i;i=e[i].pre){
		x=e[i].to;
		if(vis[x])continue;
		Count(x,-1,1,e[i].l%mod,node);
		calc(x,e[i].l%mod,1);
		Count(x,1,1,e[i].l%mod,node);
	}
	for(register int i=h[node];i;i=e[i].pre){
		x=e[i].to;
		if(vis[x])continue;
		mx=inf,all=siz[x],getroot(x),solve(root);
	}
}
void dfs(int node=1){
	siz[node]=1;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(siz[x])continue;
		dfs(x),siz[node]+=siz[x];
	}
}
int main(){
	int n=read();
	mod=read();
	for(register int i=1;i<=n;++i)pw[i]=10ll*pw[i-1]%mod;
	inv[n]=INV(pw[n]);
	for(register int i=n-1;~i;--i)inv[i]=10ll*inv[i+1]%mod;
	for(register int i=1,x,y,z;i<n;++i)x=read()+1,y=read()+1,z=read(),add(x,y,z),add(y,x,z);
	dfs(),mx=inf,all=n,getroot(1),solve(root);
	printf("%I64d\n",ans);
}
```

