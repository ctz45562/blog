---
title: '洛谷 P5306 [COCI2019] Transport'
date: 2019-09-21 18:45:09
categories: 题解
tags:
	- 点分治
	- 平衡树
keywords: Transport
description: loli一场胡策让我发现自从写了快递员，我就再也写不出常规点分治了QAQ。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/transport.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/P5306)

$loli$一场胡策让我发现自从写了[快递员](https://www.luogu.org/problem/P4886)，我就再也写不出常规点分治了$QAQ$。

<!--more-->

下记$root$为分治中心，$dis_i$为$i$到$root$的距离，$sum_i$为$i$到$root$路径的点权和（不包括$root$的点权）。

对于每次分治，先把当前树内点作为起点判断是否能到根，并用平衡树存一下到达根后剩余油量。

使点$i$能到达根，就要满足$\forall j$位于路径$i\rightarrow root$，$sum_i-dis_i-sum_j+dis_j\ge 0$。

$dfs$时维护最小的$dis_j-sum_j$判断即可。

再$dfs$一遍树，以每个点作为终点，判断是否存在一条路径和自己拼起来合法。

同理，维护一个最大的$dis_j-sum_{fa_j}$即可，注意是$fa_j$的$sum$。平衡树里查一下大于等于该值减去$a_{root}$的数作为贡献。减去$a_{root}$是因为这两次统计时都没有算上$root$的点权。还要减去同一子树的点的贡献。

复杂度是$O(n\log^2 n)$的。

一开始想把剩余油量存数组里排个序`build`一棵平衡树。甚至可以把序列去重，不涉及插入删除直接搞一个$BST$出来。

`build`是$O(n)$的，于是总复杂度就成$O(n\log n)$了，然后发现排序还是带个$\log$.

然而我已经写出来了所以代码如此冗长丑陋$QAQ$：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

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
#define ls(x) ls[x]
#define rs(x) rs[x]
struct edge{
	int pre,to,l;
}e[maxn<<1];
int a[maxn],h[maxn],siz[maxn],s[maxn],ls[maxn],rs[maxn],c[maxn],cn[maxn],cnt,num,mx,root,all,len,rt;
bool vis[maxn];
long long ans,base[maxn],b[maxn],dat[maxn];
void build(int l,int r,int &node){
	if(l>r){node=0;return;}
	node=++cnt;
	int mid=l+r>>1;
	build(l,mid-1,ls(node));
	build(mid+1,r,rs(node));
	dat[node]=b[mid],c[node]=cn[mid];
	s[node]=s[ls(node)]+s[rs(node)]+c[node];
}
void clear(int node){
	if(!node)return;
	clear(ls(node)),clear(rs(node));
	ls(node)=rs(node)=0;
}
void change(long long x,int d){
	int node=rt;
	while(node){
		s[node]+=d;
		if(dat[node]==x){c[node]+=d;return;}
		if(dat[node]<x)node=rs(node);
		else node=ls(node);
	}
}
int ask(long long d){
	int node=rt,ans=0;
	while(node){
		if(dat[node]>=d)ans+=s[rs(node)]+c[node],node=ls(node);
		else node=rs(node);
	}
	return ans;
}
inline void add(int from,int to,int l){
	e[++num]=(edge){h[from],to,l};
	h[from]=num;
}
void getroot(int node,int f=0){
	int x,ma=all-siz[node];
	for(register int i=h[node];i;i=e[i].pre){
		x=e[i].to;
		if(x==f||vis[x])continue;
		getroot(x,node),ma=max(ma,siz[x]);
	}
	if(ma<mx)mx=ma,root=node;
}
void Count(int node,long long sum,long long dis,long long mi,int f=0){
	sum+=a[node];
	mi=min(mi,dis-sum);
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(vis[x]||x==f)continue;
		Count(x,sum,dis+e[i].l,mi,node);
		if(a[x]>=e[i].l&&sum+a[x]-dis-e[i].l+mi>=0)base[++len]=a[x]+sum-dis-e[i].l;
	}
}
void work(int node,int opt,long long sum,long long dis,long long mi,int f=0){
	sum+=a[node];	
	mi=min(mi,dis-sum);
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(vis[x]||x==f)continue;
		work(x,opt,sum,dis+e[i].l,mi,node);
		if(a[x]>=e[i].l&&sum+a[x]-dis-e[i].l+mi>=0)change(a[x]+sum-dis-e[i].l,opt);
	}
}
void calc(int node,long long sum,long long dis,long long ma=0,int f=0){
	ma=max(ma,dis-sum),sum+=a[node],siz[node]=1;
	ans+=ask(ma-a[root]);
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==f||vis[x])continue;
		calc(x,sum,dis+e[i].l,ma,node);
		siz[node]+=siz[x];
	}
}
int uniq(){
	sort(base+1,base+1+len);
	int pos,k=0;
	for(register int i=1;i<=len;i=pos){
		pos=i+1;
		while(pos<=len&&base[pos]==base[i])++pos;
		b[++k]=base[i],cn[k]=pos-i;
	}
	return k;
}
void solve(int node){
	int x;
	vis[node]=1,base[len=1]=0,cnt=0;
	for(register int i=h[node];i;i=e[i].pre){
		x=e[i].to;
		if(vis[x])continue;
		Count(x,0,e[i].l,0);
		if(a[x]>=e[i].l)base[++len]=a[x]-e[i].l;
	}
	ans+=len-1,len=uniq();
	build(1,len,rt);
	for(register int i=h[node];i;i=e[i].pre){
		x=e[i].to;
		if(vis[x])continue;
		work(x,-1,0,e[i].l,0);
		if(a[x]>=e[i].l)change(a[x]-e[i].l,-1);
		calc(x,0,e[i].l,e[i].l);
		work(x,1,0,e[i].l,0);
		if(a[x]>=e[i].l)change(a[x]-e[i].l,1);
	}
	clear(rt);
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
	for(register int i=1;i<=n;++i)a[i]=read();
	for(register int i=1,x,y,z;i<n;++i)x=read(),y=read(),z=read(),add(x,y,z),add(y,x,z);
	dfs(),all=n,mx=inf,getroot(1),solve(root);
	printf("%lld\n",ans);
}
```

