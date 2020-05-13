---
title: Codeforces178F3 Representative Sampling
date: 2019-11-06 17:08:35
categories: 题解
tags:
	- 字符串
	- 动态规划
	- trie树
	- 虚树
keywords: Representative Sampling
description: 翻译+一血+首篇题解+三倍经验美滋滋。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/representativesampling.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF178F3)

翻译+一血+首篇题解+三倍经验美滋滋。<img class="emoji-coda lazyload" src="https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.1/emojis/gif/13.gif" style="width:60px" alt="">

<!--more-->

造个$trie$树，考虑一个朴素的$trie$树$DP$：

设$f(i,j)$为点$i$的子树内选了$j$个结束节点的最大答案，$s(i)$为点$i$子树内结束节点的个数。

为了不让贡献重复计算只考虑$i$到其父亲的边的贡献。

转移是个树形背包：

$f(i,j)=\max\limits_{x\in son(i),k\le \min\{j,s(x)\}}\{f(x,k)+f(i,j-k)\}+\dfrac{j\times(j-1)}{2}(j\le s(i))$

复杂度最高有$O(n|a_i|k)$。

我们发现有些没有分支和结束节点的链是多余的。

具体地说，只有结束节点和它们两两之间的$lca$有用，直接造虚树。

但是这里不用真的写虚树板子，我们只需$dfs$一遍把根节点、拥有至少$2$个儿子的节点和结束节点保留下来就行了。

记$dep(i)$为点$i$在原$trie$树上的深度。虚树上的方程要稍加改动：

$f(i,j)=\max\limits_{x\in son(i),k\le \min\{j,s(x)\}}\{f(x,k)+f(i,j-k)\}+\dfrac{j\times(j-1)}{2}\times(dep(i)-dep(fa_i))(j\le s(i))$

复杂度是$O(nk)$的。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 1000005
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
int en[maxn],son[maxn][26],siz[maxn],deep[maxn],s[4005],f[4005][2005],h[4005],c[2005],num,k,cnt=1,all;
char a[505];
struct edge{int pre,to;}e[maxn];
inline void add(int from,int to){e[++num]=(edge){h[from],to},h[from]=num;}
void insert(){
	int len=strlen(a+1),node=1;
	for(register int i=1;i<=len;++i){
		if(!son(node,a[i]-'a'))son(node,a[i]-'a')=++cnt,++siz[node];
		node=son(node,a[i]-'a');
	}
	++en[node];
}
void dfs(int node=1,int dep=0,int last=0){
	if(node==1||siz[node]>1||en[node])deep[++all]=dep,add(last,all),last=all,s[all]=en[node];
	for(register int i=0;i<26;++i)if(son(node,i))dfs(son(node,i),dep+1,last);
}
void dp(int node=1,int fa=0){
	f[node][0]=0;
	for(register int i=h[node],x;i;i=e[i].pre){
		dp(x=e[i].to,node),s[node]+=s[x];
		for(register int j=min(s[node],k);j;--j)
			for(register int k=min(s[x],j);k;--k)
				f[node][j]=max(f[node][j],f[x][k]+f[node][j-k]);
	}
	for(register int i=1;i<=s[node];++i)f[node][i]+=c[i]*(deep[node]-deep[fa]);
}
int main(){
	int n=read();
	k=read();
	for(register int i=1;i<=n;++i)scanf("%s",a+1),insert();
	for(register int i=1;i<=k;++i)c[i]=i*(i-1)>>1;
	dfs(),dp();
	printf("%d\n",f[1][k]);
}
```

