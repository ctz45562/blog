---
title: Codeforces1037H Security
date: 2019-04-28 20:50:20
categories: 题解
tags:
	- 字符串
	- 后缀自动机
	- 线段树合并
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/security.jpg
keywords: Security
description: 说实话做这个题之前我都不知道CF有H题
---

[传送门](https://www.luogu.org/problemnew/show/CF1037H)

~~说实话做这个题之前我都不知道CF有H题~~

<!--more-->

字典序尽量小又要严格大于模式串，记模式串为$S$，则贪心让前面最多的字符和$S$相同，在后面再补一个比$S$大且最小的字符。

则最优的方案一定是在$S$后面补一个尽可能小的字符。如果补不了，就倒着枚举位置，如果当前位置$i$能替换为一个比$S_i$大的字符，找到最小的可替换字符$c$换掉它。答案就是$S(1\sim i-1)+'c'$。

以下用“好点”表示包含$[L,R]$子串的节点。

先把母串的$SAM$造出来。

对每个模式串，找出仅走“好点”，能匹配的最大长度$max\underline{}len$。同时找出每个位置$i$最小的字符$nex$，满足$c>S_i$且从$i-1$匹配的节点走$nex$边的儿子为“好点”（没有为$-1$）。

从$max\underline{}len$倒着枚举，找到第一个不为$-1$的位置$i$，$S(1\sim i-1)+nex_i$即为答案。

至于怎么判断一个节点是否为“好点”，用线段树合并获取每个节点$endpos$的所有元素。假设当前走到的长度为$i$，$endpos$中存在$pos$满足$pos\in [L,R]$且$pos-i+1\in [L,R]$的节点为“好点”。合起来就是$pos\in [L+i-1,R]$。

空间$O(n\log n)$，时间$O(26n\log n)$（实际情况远跑不满）

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 400005
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
#define ls(x) ls[x]
#define rs(x) rs[x]
int h[maxn],root[maxn],ls[maxn<<5],rs[maxn<<5],all,num;
int son[maxn][26],len[maxn],fa[maxn],nex[maxn],last=1,cnt=1,n,N;
char s[maxn];
struct edge{
	int pre,to;
}e[maxn];
inline void add(int from,int to){
	e[++num].pre=h[from],h[from]=num,e[num].to=to;
}
void modify(int poi,int l,int r,int &node){
	node=++all;
	if(l==r)return;
	int mid=l+r>>1;
	if(poi<=mid)modify(poi,l,mid,ls(node));
	else modify(poi,mid+1,r,rs(node));	
}
int merge(int x,int y,int l,int r){
	if(!x||!y||l==r)return x|y;
	int ne=++all,mid=l+r>>1;
	ls(ne)=merge(ls(x),ls(y),l,mid);
	rs(ne)=merge(rs(x),rs(y),mid+1,r);
	return ne;
}
bool ask(int L,int R,int l,int r,int node){
	if(!node)return 0;
	if(L<=l&&R>=r)return 1;
	int mid=l+r>>1;
	if(L<=mid&&ask(L,R,l,mid,ls(node)))return 1;
	if(R>mid&&ask(L,R,mid+1,r,rs(node)))return 1;
	return 0;
}//询问endpos在[L,R]中是否有元素
void insert(int c){
	int p=last,ne=last=++cnt;
	modify(len[ne]=len[p]+1,1,N,root[ne]);
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
void dfs(int node=1){
	int x;
	for(register int i=h[node];i;i=e[i].pre)
		x=e[i].to,dfs(x),root[node]=merge(root[node],root[x],1,N);
}//求endpos集合
int main(){
	scanf("%s",s+1),N=strlen(s+1);
	for(register int i=1;i<=N;++i)insert(s[i]-'a');
	for(register int i=2;i<=cnt;++i)add(fa[i],i);
	dfs();
	int m=read(),l,r,node,x,i;
	while(m--){
		l=read(),r=read(),node=1;
		scanf("%s",s+1),n=strlen(s+1);
		for(i=1;;++i){
			nex[i]=-1;
			for(register int j=max(s[i]-'a'+1,0);j<26;++j){
				x=son(node,j);
				if(x&&ask(l+i-1,r,1,N,root[x])){
					nex[i]=j;
					break;
				}
			}
			x=son(node,s[i]-'a');
			if(!x||i==n+1||!ask(l+i-1,r,1,N,root[x]))break;
			node=x;
		}
		while(i&&nex[i]==-1)--i;
		if(!i)puts("-1");//找不到，无解
		else {
			for(register int j=1;j<i;++j)px(s[j]);
			px(nex[i]+'a');
			pn;
		}
	}
}
```

