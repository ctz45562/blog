---
title: Codeforces271D Good Substrings
date: 2019-04-30 08:28:06
categories: 题解
tags:
	- 字符串
	- 动态规划
	- 后缀自动机
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/goodsubstrings.jpg
keywords: Good Substrings
description: 十分板子的SAM题了。没有SAM题解赶紧水一发。
---

[传送门](https://www.luogu.org/problemnew/show/CF271D)

十分板子的$SAM$题了。

没有$SAM$题解赶紧水一发。

<!--more-->

把不好的字母的转移边权值视为$1$，好的字母权值视为$0$，这个题就是求$SAM$上从根节点出发，有多少条路径长度$\le k$。

设$f(i,j)$表示从节点$i$出发，有多少条路径长度为$j$。

方程：$f(i,j)=\sum f(son(i,c),j-ba[c])$（$ba[c]$表示$c$是否为不好的字母）

还有一种路径是直接从$i$到它的儿子的，对于$i$的每一条存在的转移边，$++f(i,ba[c])$即可。

答案为$\sum\limits_{i=0}^{k}f(1,i)$。因为$SAM$包含的子串不重不漏，所以求出来的也就本质不同。

复杂度看起来有点假，实际上分析一波就会发现转移次数不超过边数，单次转移是$O(n)$的，总复杂度$O(n^2)$

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 5005
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
int son[maxn][26],fa[maxn],len[maxn],f[maxn][1505],n,m,k,last=1,cnt=1;
bool vis[maxn],ba[26];
char s[maxn];
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
			fa[sp]=fa[q],len[sp]=len[p]+1,fa[q]=fa[ne]=sp;
			while(p&&son(p,c)==q)son(p,c)=sp,p=fa[p];
		}
	}
}
void dp(int node=1){
	vis[node]=1;
	int x;
	for(register int i=0;i<26;++i){
		x=son(node,i);
		if(!x)continue;
		if(!vis[x])dp(x);
		++f[node][ba[i]];
		for(register int j=0;j<=k;++j)
			if(j-ba[i]>=0)f[node][j]+=f[x][j-ba[i]];
	}
}
int main(){
	scanf("%s",s+1),n=strlen(s+1);
	for(register int i=1;i<=n;++i)insert(s[i]-'a');
	scanf("%s",s+1);
	for(register int i=0;i<26;++i)ba[i]=(s[i+1]=='0');
	k=read(),dp();
	int ans=0;
	for(register int i=0;i<=k;++i)ans+=f[1][i];
	printf("%d\n",ans);
}

```

