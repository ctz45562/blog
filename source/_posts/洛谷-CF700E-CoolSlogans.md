---
title: Codeforces700E Cool Slogans
date: 2019-05-02 21:53:29
categories: 题解
tags:
	- 字符串
	- 动态规划
	- 后缀自动机
	- 线段树合并
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/coolslogans.jpg
keywords: Cool Slogans
description: 洛咕的翻译简洁明了清晰易懂，我只用了半小时就明白了题意
---

[传送门](https://www.luogu.org/problemnew/show/CF700E)

洛咕的翻译简洁明了清晰易懂，我只用了半小时就明白了题意。

<!--more-->

概括一下题意：

> 给定一个字符串$S$，构造一个**字符串**数组$str$，满足任意$str[i]$为$S$的一个子串，且$str[i]$在$str[i-1]$中出现至少$2$次。求$str$数组最大大小。

如果$SAM$的节点$A$的某个串在节点$B$的某个串中出现了至少两次，则**任意**一个$B$的$endpos$，$A$在$[endpos-len[B]+len[A],endpos]$都有至少$2$个$endpos$

所以没必要每个$endpos$都检验一遍，任取一个查询即可，线段树合并实现。

我们把$str$数组倒过来，满足条件为$str[i]$在$str[i+1]$中出现至少$2$次，答案不会变。

设$f(i)$为以点$i$的串为最后一个串，数组最大大小。显然如果它在某个点的串出现过至少$2$次就可以$+1$转移过去。然而待检验的点是$O(n)$的，肯定是不行的。

举个栗子：`ab`可以向`ababa`转移，但是这个转移是不优的。明显转移到`abab`会更好（它比`ababa`短，就更容易转移向其他的点）。那么一个点转移它的子孙是最优的。

转移的时候记录$str$数组最后一个串是哪个点，判断的时候要用该点的$endpos$。

> 妙啊！

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 500005
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
#define son(x,y) son[x][y]
int ls[maxn<<5],rs[maxn<<5],root[maxn],all;
int son[maxn][26],fa[maxn],len[maxn],pos[maxn],f[maxn],tax[maxn],cur[maxn],top[maxn],last=1,cnt=1,n;
char s[maxn];
void modify(int poi,int l,int r,int &node){
	node=++all;
	if(l==r)return;
	int mid=l+r>>1;
	if(poi<=mid)modify(poi,l,mid,ls(node));
	else modify(poi,mid+1,r,rs(node));
}
int ask(int L,int R,int l,int r,int node){
	if(!node)return 0;
	if(L<=l&&R>=r)return 1;
	int mid=l+r>>1;
	if(L<=mid&&ask(L,R,l,mid,ls(node)))return 1;
	if(R>mid&&ask(L,R,mid+1,r,rs(node)))return 1;
	return 0;
}
int merge(int x,int y,int l,int r){
	if(!x||!y)return x|y;
	int mid=l+r>>1,ne=++all;
	ls(ne)=merge(ls(x),ls(y),l,mid);
	rs(ne)=merge(rs(x),rs(y),mid+1,r);
	return ne;
}
void insert(int c){
	int p=last,ne=last=++cnt;
	modify(pos[ne]=len[ne]=len[p]+1,1,n,root[ne]);
	while(p&&!son(p,c))son(p,c)=ne,p=fa[p];
	if(!p)fa[ne]=1;
	else {
		int q=son(p,c);
		if(len[q]==len[p]+1)fa[ne]=q;
		else {
			int sp=++cnt;
			memcpy(son[sp],son[q],sizeof son[q]);
			fa[sp]=fa[q],len[sp]=len[p]+1,fa[q]=fa[ne]=sp;
			while(p&&son(p,c)==q)son(p,c)=sp,p=fa[p];
		}
	}
}
int main(){
	int x,y,ans=1;
	n=read(),scanf("%s",s+1);
	for(register int i=1;i<=n;++i)insert(s[i]-'a');
	for(register int i=1;i<=cnt;++i)++tax[len[i]];
	for(register int i=1;i<=n;++i)tax[i]+=tax[i-1];
	for(register int i=1;i<=cnt;++i)cur[tax[len[i]]--]=i;
	for(register int i=cnt;i;--i)if(y=fa[x=cur[i]])root[y]=merge(root[y],root[x],1,n),pos[y]=pos[x];
	for(register int i=2;i<=cnt;++i){
		y=top[fa[x=cur[i]]];
		if(!y){f[x]=1,top[x]=x;continue;}
		if(ask(pos[x]-len[x]+len[fa[y]],pos[x]-1,1,n,root[y]))ans=max(ans,f[x]=f[y]+1),top[x]=x;
		else top[x]=y;
	}
	printf("%d\n",ans);
}

```

