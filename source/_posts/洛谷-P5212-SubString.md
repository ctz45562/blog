---
title: 洛谷 P5212 SubString
date: 2019-04-26 10:41:35
categories: 题解
tags:
	- 字符串
	- 数据结构
	- 后缀自动机
	- LCT
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/substring.jpg
keywords: SubString
description: 我的第一篇SAM题解QwQ 
---

[传送门](https://www.luogu.org/problemnew/show/P5212)

我的第一篇$SAM$题解$QwQ$

<!--more-->

看到动态加字符就能想到$SAM$上去。

对于查询答案就是跑一遍串，到达点的$endpos$集合大小。

动态维护$endpos$集合大小。最朴素的求法就是在$parent\ tree$上$DP$。那就维护一下子树大小，添加字符的过程中会涉及到改$fa$，直接上$LCT$维护子树大小。

>  ~~@asuldb：为啥LCT​不方便维护子树啊。。。就一个子树和多好维护，比链加好写还跑得快~~

半小时一遍$A$美滋滋$QwQ$

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 1200005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
struct Link_Cut_Tree{
#define son(x,y) son[x][y]
#define ls(x) son(x,0)
#define rs(x) son(x,1)
#define root(x) (son[fa[x]][0]==x||son[fa[x]][1]==x)
#define whson(x) (son[fa[x]][1]==x)
	int son[maxn][2],fa[maxn],dat[maxn],sum[maxn],_sum[maxn],rev[maxn],st[maxn];
    //_sum是虚子树大小
	inline void update(int node){
		sum[node]=sum[ls(node)]+sum[rs(node)]+_sum[node]+dat[node];
	}
	inline void addedge(int s,int f,int wh){
		if(s)fa[s]=f;
		son(f,wh)=s;
	}
	inline void reverdown(int x){
		swap(son(x,0),son(x,1)),rev[x]^=1;
	}
	inline void pushdown(int x){
		if(rev[x]){
			if(ls(x))reverdown(ls(x));
			if(rs(x))reverdown(rs(x));
			rev[x]=0;
		}
	}
	inline void zhuan(int x){
		int f=fa[x],gf=fa[f],wh=whson(x);
		fa[x]=gf;
		if(root(f))son(gf,whson(f))=x;
		addedge(son(x,wh^1),f,wh);
		addedge(f,x,wh^1);
		update(f),update(x);
	}
	void splay(int x){
		int y=x,top=1;
		st[1]=x;
		while(root(y))st[++top]=y=fa[y];
		while(top)pushdown(st[top--]);
		while(root(x)){
			y=fa[x];
			if(root(y))zhuan(whson(x)^whson(y)?x:y);
			zhuan(x);
		}
	}
	void access(int x){
		for(int y=0;x;y=x,x=fa[x])
			splay(x),_sum[x]+=sum[son(x,1)]-sum[y],son(x,1)=y,update(x);
	}
	void makeroot(int x){
		access(x),splay(x),reverdown(x);
	}
	void cut(int x,int y){
		makeroot(x),access(y),splay(y);
		fa[x]=son(y,0)=0,update(y);
	}
	void link(int x,int y){
		access(x),splay(x),makeroot(y),fa[x]=y,_sum[y]+=sum[x],update(y);
	}
	int ask(int x){
		makeroot(1),access(x),splay(x);
		return sum[son(x,1)]+_sum[x]+dat[x];
	}
	inline void init(int x){
		dat[x]=sum[x]=1;
	}
}lct;
int son[maxn][26],fa[maxn],len[maxn],last=1,cnt=1,n,mask;
char s[3000005];
inline void change_father(int x,int y){
	if(fa[x])lct.cut(x,fa[x]);
	lct.link(x,fa[x]=y);
}
void insert(int c){
	int p=last,ne=last=++cnt;
	len[ne]=len[p]+1,lct.init(ne);
	while(p&&!son(p,c))son(p,c)=ne,p=fa[p];
	if(!p)change_father(ne,1);
	else {
		int q=son(p,c);
		if(len[q]==len[p]+1)change_father(ne,q);
		else {
			int sp=++cnt;
			memcpy(son[sp],son[q],sizeof son[q]);
			change_father(sp,fa[q]),len[sp]=len[p]+1;
			change_father(q,sp),change_father(ne,sp);
			while(p&&son(p,c)==q)son(p,c)=sp,p=fa[p];
		}
	}
}
void work(){
	scanf("%s",s),n=strlen(s);
	return;
	int rec=mask;
	for(register int i=0;i<n;++i){
		rec=(rec*131+i)%n;
		swap(s[rec],s[i]);
	}
}
void ask(){
	int node=1,ans=0;
	work();
	for(register int i=0;i<n&&node;++i)node=son(node,s[i]-'A');
	if(!node)puts("0");
	else printf("%d\n",ans=lct.ask(node));
	mask^=ans;
}
void add(){
	work();
	for(register int i=0;i<n;++i)insert(s[i]-'A');
}
int main(){
	int t=read();
	char op[10];
	scanf("%s",s+1),n=strlen(s+1);
	for(register int i=1;i<=n;++i)insert(s[i]-'A');
	while(t--){
		scanf("%s",op);
		if(op[0]=='Q')ask();
		else add();
	}
}

```

