---
title: Codeforces822E Liar
date: 2019-11-11 20:43:46
categories: 题解
tags:
    - 字符串
    - 动态规划
    - 哈希
    - 二分
    - 后缀数组
    - ST表
keywords: Liar
description: 来啊，卡我啊，我换SA了你再卡我？
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/liar.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF822E)

> 来啊，卡我啊，我换$SA$了有本事你再卡我？{% emoji_coda quyin/angry %}

<!--more-->

容易想到设$f(i,j)$为$s$的前$i$位中选$j$个子串匹配$t$的最大长度。

枚举分段长度转移：$f(i,j)=\max\{f(i-1,j),f(i-k,j-1)+k\}(s[i-k+1,i]=t[f(i-k,j-1)+1,f(i-k,j-1)+k])$

为了方便优化改成刷表的形式：

$\begin{cases}f(i,j)+k\rightarrow f(i+k,j+1)(s[i+1,i+k]=t[f(i,j)+1,f(i,j)+k])\\f(i,j)\rightarrow f(i+1,j)\end{cases}$

假设有两个可行的转移$i+k_1,i+k_2(k_1<k_2)$。在$i+k_1$会存在一个的转移到$i+k_2$（即分为两段$[i+1,k_1],[i+k_1+1,k_2]$），这样多分了一段，一定不优。

所以直接转移到$i+1$开始最大匹配长度即可，哈希+二分。

然而。。。这个。。。这个丧心病狂的CF它卡哈希：{% emoji_coda luotianyi/无言以对 %}

![](\images\liar-1.png)


逼我强行改成后缀数组。~~居然还能默写对真是神奇~~

哈希+二分复杂度是$O(nx\log n)$的，后缀数组是$O(n\log n+nx)$的。

代码：
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
int f[maxn][31],tax[maxn<<1],sa[maxn<<1],rk[maxn<<1],tp[maxn<<1],hei[maxn<<1],g[maxn<<1][21],lg[maxn<<1],m,n;
char a[maxn],s[maxn<<1];
void Rsort(){
	for(register int i=0;i<=m;++i)tax[i]=0;
	for(register int i=1;i<=n;++i)++tax[rk[i]];
	for(register int i=1;i<=m;++i)tax[i]+=tax[i-1];
	for(register int i=n;i;--i)sa[tax[rk[tp[i]]]--]=tp[i];
}
void Ssort(){
	for(register int i=1;i<=n;++i)rk[i]=s[i],tp[i]=i;
	m=225,Rsort();
	for(register int k=1,p=0;p<n;k<<=1,m=p){
		p=0;
		for(register int i=1;i<=k;++i)tp[++p]=n-k+i;
		for(register int i=1;i<=n;++i)if(sa[i]>k)tp[++p]=sa[i]-k;
		Rsort();
		for(register int i=1;i<=n;++i)tp[i]=rk[i];
		rk[sa[1]]=p=1;
		for(register int i=2;i<=n;++i)
			rk[sa[i]]=tp[sa[i]]==tp[sa[i-1]]&&tp[sa[i]+k]==tp[sa[i-1]+k]?p:++p;
	}
}
void get_height(){
	int k=0,x;
	for(register int i=1;i<=n;++i){
		if(rk[i]==1)continue;
		if(k)--k;
		x=sa[rk[i]-1];
		while(x+k<=n&&i+k<=n&&s[x+k]==s[i+k])++k;
		g[rk[i]][0]=hei[rk[i]]=k;
	}
}
void ST(){
	for(register int i=2;i<=n;++i)lg[i]=lg[i>>1]+1;
	for(register int j=1;j<=lg[n];++j)
		for(register int i=1;i+(1<<j-1)<=n;++i)
			g[i][j]=min(g[i][j-1],g[i+(1<<j-1)][j-1]);
}
inline int query(int l,int r){
	int len=lg[r-l+1];
	return min(g[l][len],g[r-(1<<len)+1][len]);
}
inline int lcp(int i,int j){
	if(rk[i]>rk[j])swap(i,j);
	return query(rk[i]+1,rk[j]);
}
int main(){
	int k,N=read(),M;
	scanf("%s",a+1);
	for(register int i=1;i<=N;++i)s[i]=a[i];
	s[n=N+1]='$',M=read(),scanf("%s",a+1);
	if(N<M){puts("NO");return 0;}
	for(register int i=1;i<=M;++i)s[n+i]=a[i];
	n+=M,Ssort(),get_height(),ST(),k=read();
	for(register int i=0;i<N;++i)
		for(register int j=0;j<=k;++j){
			f[i+1][j]=max(f[i+1][j],f[i][j]);
			if(j==k)continue;
			int len=lcp(i+1,f[i][j]+N+2);
			if(len)f[i+len][j+1]=max(f[i+len][j+1],f[i][j]+len);
		}
	for(register int i=1;i<=k;++i)if(f[N][i]==M){puts("YES");return 0;}
	puts("NO");
}
```
