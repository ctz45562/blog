---
title: Codeforces433E Tachibana Kanade's Tofu
date: 2019-10-15 20:26:27
categories: 题解
tags:
	- 动态规划
	- 字符串
	- AC自动机
	- 数位DP
keywords: Tachibana Kanade's Tofu
description: 快 乐 水 题 
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/tachibanakanadestofu.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF433E)

快 乐 水 题 {% emoji_coda luotianyi/吃包群众 %}

<!--more-->

这翻译跟那啥一样。。。复述一遍题意：

> 给定$n$个$m$进制数（可能包含前导$0$），每个数有一个价值$v_i$。定义一个$m$进制数的权值为$\sum\limits_{i=1}^ncnt_i\times v_i$，其中$cnt_i$为第$i$个数在该数中作为子串出现的次数。求$[L,R]$中（$L,R$均为$m$进制数），权值不超过$K$且不含前导零的$m$进制数的个数。

很套路的$AC$自动机上数位$DP$。

设$f(i,0/1,0/1,j,k)$为填到第$i$位、是否顶上界、是否有前导零、位于$AC$自动机的$j$节点、权值为$k$的方案数。

枚举数字刷表转移即可。

转移时要特别注意前导零。如果转移后的状态依然有前导零，权值$k$不会改变，**$AC$自动机也不会往下走**，因为这点调了一个小时{% emoji_coda quyin/hematemesis %}。

复杂度$O(4lenK\sum|S|m)$，算出来是$O(1.6\times 10^9)$，刷表时跳过不合法状态再加上滚动数组就能过了。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 205
#define inf 0x3f3f3f3f

const int mod = 1e9 + 7;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
#define son(x,y) son[x][y]
int en[maxn],line[maxn],son[maxn][20],pre[maxn],L[maxn],R[maxn],s[maxn],f[2][2][2][maxn][505],cnt=1,m,K;
void insert(int len,int v){
	int node=1;
	for(register int i=1;i<=len;++i){
		if(!son(node,s[i]))son(node,s[i])=++cnt;
		node=son(node,s[i]);
	}
	en[node]+=v;
}
void build(){
	int head=0,tail=1;
	line[1]=1;
	for(register int i=0;i<m;++i)son(0,i)=1;
	while(head<tail){
		int node=line[++head],x;
		en[node]+=en[pre[node]];
		for(register int i=0;i<m;++i){
			x=son(node,i);
			if(x)pre[x]=son(pre[node],i),line[++tail]=x;
			else son(node,i)=son(pre[node],i);
		}
	}
}
inline void add(int &x,int y){x+=y;if(x>=mod)x-=mod;}
int solve(int *tp){
	int len=tp[0],ans=0;
	memset(f,0,sizeof f);
	f[len&1][1][1][1][0]=1;
	for(register int i=len;i;--i)
		for(register int j=0;j<2;++j)
			for(register int k=0;k<2;++k)
				for(register int l=1;l<=cnt;++l)
					for(register int p=0;p<=K;++p){
						if(!f[i&1][j][k][l][p])continue;
						for(register int q=j?tp[i]:m-1;~q;--q)
							if((k&&!q)||p+en[son(l,q)]<=K)
								add(f[i&1^1][j&&q==tp[i]][k&&!q][(k&&!q)?l:son(l,q)][(k&&!q)?p:p+en[son(l,q)]],f[i&1][j][k][l][p]);
						f[i&1][j][k][l][p]=0;
					}
	for(register int i=1;i<=cnt;++i)
		for(register int j=0;j<=K;++j)
			add(ans,(f[0][0][0][i][j]+f[0][1][0][i][j])%mod);
	return ans;
}
int main(){
	int n=read();
	m=read(),K=read(),L[0]=read();
	for(register int i=1;i<=L[0];++i)L[L[0]-i+1]=read();
	R[0]=read();
	for(register int i=1;i<=R[0];++i)R[R[0]-i+1]=read();
	while(n--){
		int len=read();
		for(register int i=1;i<=len;++i)s[i]=read();
		insert(len,read());
	}
	build();
	int node=1,ans,sum=0;
	for(register int i=L[0];i;--i)node=son(node,L[i]),sum+=en[node];
	ans=sum<=K;
	printf("%d\n",(solve(R)-solve(L)+ans+mod)%mod);
}

```

