---
title: Codeforcesの三棵树
date: 2019-11-12 20:07:29
categories: 题解
tags:
    - 构造
    - 贪心
    - 枚举
    - 动态规划
    - 换根DP
keywords: 树
description: 在洛谷某讨论上薅了三道挺有意思的树题过来
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/codeforcesdsks.jpg
mathjax: true
---

在洛谷某讨论上薅了三道挺有意思的树题过来，都是$CF$的题。

话说我最近做的题和写的题解基本都是$CF$的。。。

<!--more-->

---

## [Codeforces1041E Tree Reconstruction](https://www.luogu.org/problem/CF1041E)

第一次做这种构造题。。。应该是吧。

显然给出的点对其中一个必定是$n$，否则就假了。

按另一个点排序，钦定$n$为根节点（构造完再加进去），尝试在一条链上构造答案。以下记$a_i$为点对中不是$n$的那个点。

- 若$a_i\neq a_{i-1}$，直接把链顶父亲设为$a_i$，这样对所有存在的点都没有影响。
- 若$a_i=a_{i-1}$，需要再来一条边割掉后一边最大值为$a_i$。钦定一个不在链里的点$x$，链顶父亲设为$x$。如果找不到就$gg$了。

最后把链顶的父亲设为$n$。

数据范围还挺友善的，$O(n^2)$暴力枚举就能过。写个链表的话就$O(n)$了。

关于正确性，构造成一条链是最优的。简单证一下：

假设我们已经得到了一棵合法的树（不一定是链），把$n$当做根节点，$a_i$实际上就是子树最大值。

考虑合并挂在某个点下面的两条链。把里面的边按$a_i$排序，小的$a_i$挂在大的$a_i$下面，就能得到一条新的链。不断地合并，最后这棵树就会变成一条合法的链。

所以只要存在合法的树，一定存在合法的链。那么构造成链是最优的。


代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
 
#define maxn 1005
#define inf 0x3f3f3f3f
 
using namespace std;
 
inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int a[maxn],fa[maxn];
bool vis[maxn];
int main(){
	int n=read(),tp=0;
	for(register int i=1,x,y;i<n;++i){
		x=read(),y=read();
		if(max(x,y)!=n){puts("NO");return 0;}
		a[i]=min(x,y);
	}
	sort(a+1,a+n);
	for(register int i=1;i<n;++i){
		if(!vis[a[i]])fa[tp]=a[i],tp=a[i],vis[a[i]]=1;
		else {
			bool flag=0;
			for(register int j=1;j<a[i];++j)
				if(!vis[j]){
					flag=vis[j]=1,fa[tp]=j,tp=j;
					break;
				}
			if(!flag){puts("NO");return 0;}
		}
	}
	fa[tp]=n,puts("YES");
	for(register int i=1;i<n;++i)printf("%d %d\n",i,fa[i]);
}
```

---

## [Codeforces911F Tree Destruction](https://www.luogu.org/problem/CF911F)

真的，没有人体会过瞎猜一波结论、写了代码、一发$AC$的感觉。

大力猜测先把直径上的侧链删干净，最后把直径删掉。

证明胡一波：

考虑每个点被删除时的贡献，一定要找一个最远的点作为同伙，然后把自己干掉。

最远的点就是直径两端点之一了。为了让每个不在直径上的点被删掉时的贡献尽可能大，直径给其他点当梯子，最后再删掉。

胡毕。

比较懒复杂度是由于$LCA$的$O(n\log n)$。把$LCA$换成两遍$dfs$就能$O(n)$做。

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
int son[maxn],siz[maxn],top[maxn],deep[maxn],fa[maxn],h[maxn],vis[maxn],line[maxn],deg[maxn],num,ma,root;
pair<int,int>ans[maxn];
struct edge{int pre,to;}e[maxn<<1];
inline void add(int from,int to){++deg[from],e[++num]=(edge){h[from],to},h[from]=num;}
void dfs1(int node=1){
	siz[node]=1;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(siz[x])continue;
		deep[x]=deep[node]+1,fa[x]=node;
		dfs1(x),siz[node]+=siz[x];
		if(siz[x]>siz[son[node]])son[node]=x;
	}
}
void dfs2(int node=1){
	siz[node]=0;
	if(!son[node])return;
	top[son[node]]=top[node],dfs2(son[node]);
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(siz[x])top[x]=x,dfs2(x);
	}
}
inline int lca(int x,int y){
	while(top[x]!=top[y])deep[top[x]]<deep[top[y]]?y=fa[top[y]]:x=fa[top[x]];
	return deep[x]<deep[y]?x:y;
}
inline int dis(int x,int y){return deep[x]+deep[y]-(deep[lca(x,y)]<<1);}
void dfs(int node,int f=0,int len=0){
	if(len>ma)ma=len,root=node;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==f)continue;
		dfs(x,node,len+1);
	}
}
void _dfs(int node=1){
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==fa[node])continue;
		_dfs(x),vis[node]+=vis[x];
	}
}
int main(){
	int n=read(),head=0,tail=0,len=0;
	for(register int i=1,x,y;i<n;++i)x=read(),y=read(),add(x,y),add(y,x);
	dfs1(),dfs2(),dfs(1);
	int node=root;
	ma=0,dfs(root);
	++vis[node],++vis[root],--vis[lca(node,root)],--vis[fa[lca(node,root)]],_dfs();
	long long res=1ll*ma*(ma+1)>>1;
	for(register int i=1;i<=n;++i)if(!vis[i]&&deg[i]==1)line[++tail]=i;
	while(head<tail){
		int x=line[++head],u=dis(x,node),v=dis(x,root);
		ans[++len]=(pair<int,int>){x,u>v?node:root},res+=max(u,v);
		for(register int i=h[x];i;i=e[i].pre)
			if(!vis[e[i].to]&&--deg[e[i].to]==1)line[++tail]=e[i].to;
	}
	head=lca(node,root);
	while(node!=head||root!=head){
		if(deep[node]<deep[root])swap(node,root);
		ans[++len]=(pair<int,int>){node,root},node=fa[node];
	}
	printf("%I64d\n",res);
	for(register int i=1;i<=len;++i)printf("%d %d %d\n",ans[i].first,ans[i].second,ans[i].first);
}
```

---

## [Codeforces708C Centroids](https://www.luogu.org/problem/CF708C)

下记$siz$为子树大小。

如果一个点本来就是中心，就不要管它。

否则以这个点为根，有且仅有它的一个儿子的$siz$大于$\left\lfloor\dfrac{n}{2}\right\rfloor$，记这个点为$x$。

以下是我从上午到下午的$zz$的做题历程：

断一条边，加一条边，实质上就是把一个子树接到另一个点上。

我们需要从$x$子树中选一棵子树接到一个另一个点上。选的子树需要满足既能使$x$子树变合法，又能不让被接上去的点$siz$依然小于$\left\lfloor\dfrac{n}{2}\right\rfloor$。

最优方案就是从$x$子树所有点的$siz$中，找到$siz_x-\left\lfloor\dfrac{n}{2}\right\rfloor$的后继，接到根节点$siz$**最小的儿子**上。（从这开始全乱了）

由于要判断所有点为根时的情况，考虑换根思想。

从$x$转移到儿子$y$，去掉一个$siz_y$，加上一个$n-siz_y$，bulabulabula

。。。

这咋搞啊，树剖套主席树时间爆炸，线段树合并空间爆炸。~~关键是不想写~~

。。。

噫，好！我会了！可以按$dfs$序建主席树，再开一棵线段树，$dfs$一遍树进入时去掉该点的$siz$加上$n-siz$，出去时再改回来bulabulabula

真的难写。码完了过不去样例，发现我压根不用接到最小的儿子上，直接接到根节点就好了。

于是这个问题转化为不合法的子树中是否存在一个点$siz\le \left\lfloor\dfrac{n}{2}\right\rfloor$

刚才的线段树+主席树也是可以做的，但是调不动<span class="spoiler">影响我的颓废时间</span>。考虑$DP$。

---

**正迟但到：**

设$f(i)$为点$i$的子树所有点的$siz$中，$\left\lfloor\dfrac{n}{2}\right\rfloor+1$的前驱。

转移：$f(i)=\begin{cases}siz_i&siz_i\le \left\lfloor\dfrac{n}{2}\right\rfloor\\\max\limits_{j\in son(i)}\{f(j)\}&otherwise\end{cases}$

考虑换根$DP$。

假设我们已经得到了$x$为根时的$f(fa_x)$记为$F$，把根从$x$移动到儿子$y$，需要知道以$y$为根时的$f(x)$：

- 对$x$的影响：少了一个儿子$y$，$siz$更新为$n-siz_y$。
- 对$y$的影响：多了一个儿子$x$，$siz$更新为$n$。
  
维护每个点儿子中$f$的最大值$m1$和次大值$m2$。

- 如果$n-siz_y\le \left\lfloor\dfrac{n}{2}\right\rfloor$，$f(x)=n-siz_y$
- 否则，$\begin{cases}f(x)=\max\{F,m2\}&f(y)=m1\\f(x)=\max\{F,m1\}&otherwise\end{cases}$

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 400005
#define inf 0x3f3f3f3f
#define px putchar
#define pn px('\n')

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int siz[maxn],f[maxn],m1[maxn],m2[maxn],h[maxn],ms[maxn],num,n,lim;
bool ans[maxn];
struct edge{int pre,to;}e[maxn<<1];
inline void add(int from,int to){e[++num]=(edge){h[from],to},h[from]=num;}
inline void check(int &f1,int &f2,int d){
	if(d>f1)f2=f1,f1=d;
	else if(d>f2)f2=d;
}
void dp(int node=1,int fa=0){
	siz[node]=1;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==fa)continue;
		dp(x,node);
		check(m1[node],m2[node],f[x]);
		f[node]=max(f[node],f[x]),siz[node]+=siz[x];
		if(siz[x]>siz[ms[node]])ms[node]=x;
	}
	if(siz[ms[node]]<n-siz[node])ms[node]=-1;
	if(siz[node]<=lim)f[node]=siz[node];
}
inline int Max(int node){return ms[node]==-1?n-siz[node]:siz[ms[node]];}
void _dp(int node=1,int fa=0,int F=0){
	if(Max(node)>lim){
		if(ms[node]==-1){if(F>=Max(node)-lim)ans[node]=1;}
		else if(f[ms[node]]>=Max(node)-lim)ans[node]=1;
	}
	else ans[node]=1;
	for(register int i=h[node],x;i;i=e[i].pre){
		x=e[i].to;
		if(x==fa)continue;
		if(n-siz[x]<=lim)_dp(x,node,n-siz[x]);
		else _dp(x,node,max(m1[node]==f[x]?m2[node]:m1[node],F));
	}
}
int main(){
	n=read(),lim=n>>1;
	for(register int i=1,x,y;i<n;++i)x=read(),y=read(),add(x,y),add(y,x);
	dp(),_dp();
	for(register int i=1;i<=n;++i)printf("%d ",ans[i]);
	pn;
}
```
