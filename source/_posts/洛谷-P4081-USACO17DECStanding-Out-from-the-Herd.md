---
title: 'USACO-17DEC Standing Out from the Herd'
date: 2019-04-27 07:45:50
categories: 题解
tags:
	- 字符串
	- 后缀自动机
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/standingoutfromtheherd.jpg
keywords: Standing Out from the Herd
description: 感觉我的做法好鬼畜啊。。。是不是错了啊。
---

[传送门](https://www.luogu.org/problemnew/show/P4081)

感觉我的做法好鬼畜啊。。。是不是错了啊

不过~~可爱的~~$asuldb$跟我做法一样诶$QwQ$

<!--more-->

把所有串用特殊字符隔开，拼一块造$SAM$。

$endpos$集合就是子串出现位置，如果一个节点的$endpos$只在一个串里出现过，它就可以算进独特值里。

造$SAM$的时候可以给节点染个色，然后在$parent\ tree$上向上合并，如果某个节点只有一种颜色$x$，就可以给$x$产生贡献。

因为把所有串拼了起来，会有不是$x$的子串出现，就要记录一下最大的$endpos$值$ma$，显然$ma[i]=\max\{ma[j]\}(fa[j]=i)$，再记录一下每个串左端点的位置$ll[x]$，贡献就是$(ma[i]-len[fa[i]])-\max\{ma[i]-len[i]+1,ll[x]\}+1$

有可能该节点没有一个子串属于$x$，就会有负数，对$0$取个$\max$就好啦。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 200005
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
int h[maxn],col[maxn],ll[maxn],ma[maxn],son[maxn][27],fa[maxn],len[maxn],ans[maxn],last=1,cnt=1,all,num,n;
char s[maxn];
struct edge{
	int pre,to;
}e[maxn];
inline void add(int from,int to){
	e[++num].pre=h[from],h[from]=num,e[num].to=to;
}
inline void merge(int &x,int y){
	if(x==y)return;
	if(x==-1||y==-1||x&&y)x=-1;
	else x=x|y;
}//合并颜色
void insert(int c,int i=0){
	int p=last,ne=last=++cnt;
	len[ne]=len[p]+1,ma[ne]=len[ne],col[ne]=i;//染色
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
		x=e[i].to,dfs(x),merge(col[node],col[x]),ma[node]=max(ma[node],ma[x]);
	x=col[node];
	if(~x)ans[x]+=max((ma[node]-len[fa[node]])-max(ma[node]-len[node]+1,ll[x])+1,0);
}
int main(){
	n=read();
	int m,N=0;
	for(register int i=1;i<=n;++i){
		scanf("%s",s+1),m=strlen(s+1);
		for(register int j=1;j<=m;++j)insert(s[j]-'a',i);
		ll[i]=N+1,N+=m+1;//记录左端点
		if(i!=n)insert(26);//特殊字符
	}
	for(register int i=2;i<=cnt;++i)add(fa[i],i);
	dfs();
	for(register int i=1;i<=n;++i)printf("%d\n",ans[i]);
}

```

