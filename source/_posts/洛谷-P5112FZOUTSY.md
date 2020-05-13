---
title: 洛谷 P5112 FZOUTSY
date: 2019-05-11 20:13:27
categories: 题解
tags:
	- 字符串
	- 后缀自动机
	- 莫队
description: 不知道是莫队写假了还是人丑常数大，随便一交就是最劣解QwQ。
keywords: FZOUTSY
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/fzoutsy.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problemnew/show/P5112)

不知道是莫队写假了还是人丑常数大，随便一交就是最劣解$QwQ$。

<!--more-->

首先来看数据范围：$n^2m\le 10^{15}$

开个方：$n\sqrt{m}\le 3\times 10^7$

这不莫队吗？考虑怎么$O(1)$添加、删除一个后缀的影响。

把串翻转一下就是前缀的最长公共后缀，也就是$SAM$里$parent\ tree$的$LCA$的$len$。

而且某个点子孙的$len$一定大于它自己的$len$。

这样找到所有最高的$len\ge k$的节点，它子树中任意两个节点都能产生$1$贡献。

染个色计数跑一遍莫队就完了。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 6000005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
#define son(x,y) son[x][y]
struct Query{
	int l,r,be,num;
	bool operator < (const Query &x)const{
		if(x.be!=be)return be<x.be;
		if(be&1)return r<x.r;
		return r>x.r;
	}
}q[100005];
struct edge{
	int pre,to;
}e[maxn];
int trans[150],son[maxn][7],fa[maxn],len[maxn],c[maxn],top[maxn],h[maxn],w[maxn]={1},num,cnt=1,last=1,k;
long long ans[100005],all;
char s[maxn];
inline void add(int from,int to){
	e[++num].pre=h[from],h[from]=num,e[num].to=to;
}
void dfs(int node=1,int tp=0){
	if(!tp&&len[node]>=k)tp=node;
	top[node]=tp;
	for(register int i=h[node];i;i=e[i].pre)dfs(e[i].to,tp);
}
void insert(int c){
	int p=last,ne=last=++cnt;
	len[ne]=len[p]+1;
	while(p&&!son(p,c))son(p,c)=ne,p=fa[p];
	if(!p)fa[ne]=1;
	else {
		int q=son(p,c);
		if(len[q]==len[p]+1)fa[ne]=q;
		else {
			int sp=++cnt;
			memcpy(son[sp],son[q],sizeof son[q]);
			len[sp]=len[p]+1,fa[sp]=fa[q],fa[q]=fa[ne]=sp;
			while(p&&son(p,c)==q)son(p,c)=sp,p=fa[p];
		}
	}
}
inline void up(int x){
	if(!x)return;
	all+=(c[x]++);
}
inline void down(int x){
	if(!x)return;
	all-=(--c[x]);
}
int main(){
	trans['z']=1,trans['o']=2,trans['u']=3,trans['t']=4,trans['s']=5,trans['y']=6;
	int n=read(),m=read(),sq=n/sqrt(m),l,r;k=read();
	scanf("%s",s+1);
	for(register int i=1;i<=n>>1;++i)swap(s[i]=trans[s[i]],s[n-i+1]=trans[s[n-i+1]]);
	for(register int i=1;i<=n;++i)insert(s[i]);
	for(register int i=1;i<=n;++i)w[i]=son(w[i-1],s[i]);
	for(register int i=2;i<=cnt;++i)add(fa[i],i);
	dfs();
	for(register int i=1;i<=n;++i)w[i]=top[w[i]];
	for(register int i=1;i<=m;++i)l=read(),r=read(),q[i].l=n-r+1,q[i].r=n-l+1,q[i].num=i,q[i].be=l/sq+1;//原串翻转了，询问区间也要翻转
	sort(q+1,q+1+m);
	l=1,r=0;
	for(register int i=1;i<=m;++i){
		while(l<q[i].l)down(w[l++]);
		while(l>q[i].l)up(w[--l]);
		while(r>q[i].r)down(w[r--]);
		while(r<q[i].r)up(w[++r]);
		ans[q[i].num]=all;
	}
	for(register int i=1;i<=m;++i)printf("%lld\n",ans[i]);
}

```

$P.S$：欢迎$dalao$提出蒟蒻哪里写假了，~~虽然当最劣解也挺好的~~

