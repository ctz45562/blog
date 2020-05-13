---
title: SPOJ8791 DYNALCA - Dynamic LCA
date: 2019-02-24 11:12:46
categories: 题解
tags:
	- 数据结构
	- LCT
	- LCA
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/dynalca-dynamiclca.jpg
keywords: Dynamic LCA
description: 只要会用LCT求LCA，这个题就很简单了QwQ（虽然因为之前一直写的假的splay和LCT调了一上午）
---

[传送门](https://www.luogu.org/problemnew/show/SP8791)

只要会用$LCT$求$LCA$，这个题就很简单了$QwQ$~~（虽然因为之前一直写的假的$splay$和$LCT$调了一上午）~~

<!--more-->

看到连边删边自然会去想$LCT$，那怎么用$LCT$求$LCA$？

放图来看：

![](\images\Dynamic lca-1.png)

我要查$LCA(6,7)$，如果打通根到$6$的实链：

![](\images\Dynamic lca-2.png)

再打通到$7$的实链：

![](\images\Dynamic lca-3.png)

就会发现这两条实链在点$2$处**分开**了，也可以说这两条链一起在点$2$**汇入**进去。点$2$就是它们的$LCA$。

怎么找呢？在$access$时，每次改右儿子都相当于把两棵$splay$的链汇入进这个点。那最后一次汇入的点就是答案了。

实现：

```cpp
int access(int x){
	int y=0;
    for(;x;y=x,x=fa[x])
        splay(x),son(x,1)=y;
    return y;
}
int lca(int x,int y){
    access(x);//一定要先打通到x的实链才能保证y汇入了x的链
    return access(y);
}
```

注意一下换根的影响。$link$可以换根，因为连上爹后他自己就不是根了；而$cut$不能换根。

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 100005
#define inf 0x3f3f3f
#define pn putchar('\n')
#define px(x) putchar(x)
#define ps putchar(' ')
#define pd puts("======================")
#define pj puts("++++++++++++++++++++++")

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
template<typename T>
inline T read(){
	T x=0;
	int y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}//快读
int n;
struct Link_Cut_Tree{
	int son[maxn][2],fa[maxn],rev[maxn],st[maxn];
#define whson(x) (son[fa[x]][1]==x)
#define root(x) (son[fa[x]][0]!=x&&son[fa[x]][1]!=x)
#define son(x,y) son[x][y]
	inline void addedge(int s,int f,int wh){
		if(s)fa[s]=f;
		son(f,wh)=s;
	}
	inline void reverdown(int x){
		rev[x]^=1;
		swap(son(x,0),son(x,1));
	}
	inline void pushdown(int node){
		if(rev[node]){
			if(son(node,0))reverdown(son(node,0));
			if(son(node,1))reverdown(son(node,1));
			rev[node]=0;
		}
	}
	inline void zhuan(int x){
		int f=fa[x],gf=fa[f],wh=whson(x);
		fa[x]=gf;
		if(!root(f))son(gf,whson(f))=x;
		addedge(son(x,wh^1),f,wh);
		addedge(f,x,wh^1);
	}
	inline void splay(int x){
		int top=1,y=x;
		st[1]=x;
		while(!root(y))st[++top]=y=fa[y];
		while(top)pushdown(st[top--]);
		while(!root(x)){
			if(!root(fa[x]))
				zhuan(whson(fa[x])^whson(x)?x:fa[x]);
			zhuan(x);
		}
	}
	inline int access(int x){
		int y=0;
		for(;x;y=x,x=fa[x])
			splay(x),son(x,1)=y;
		return y;
	}
	inline void makeroot(int x){
		access(x),splay(x),reverdown(x);
	}
	inline void link(int x,int y){
		makeroot(x);
		fa[x]=y;
	}
	inline void cut(int x){
		access(x),splay(x);
		fa[son(x,0)]=0,son(x,0)=0;
	}
	inline int ask(int x,int y){
		access(x);
		return access(y);
	}
}lct;
int main(){
	n=read();
	int m=read();
	while(m--){
		char s[5];
		scanf("%s",s);
		if(s[0]=='c'){
			int x=read();
			lct.cut(x);
		}
		else {
			int x=read(),y=read();
			if(s[1]=='i')lct.link(x,y);
			else printf("%d\n",lct.ask(x,y));
		}
	}
}

```

