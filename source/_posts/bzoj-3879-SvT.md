---
title: bzoj 3879 SvT
date: 2019-08-07 16:43:51
categories: 题解
tags:
	- 字符串
	- 后缀自动机
	- 虚树
keywords: SvT
description: SvT=SAM+Vitual Tree？（猜的）
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/svt.jpg
mathjax: true
---

[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=3879)

$SvT=SAM+Vitual\ Tree$？（猜的）

<!--more-->

其实是比较套路的$SAM$+虚树。把串反过来建$SAM$，找出每个后缀对应的节点，求在$parent\ tree$上两两$LCA$的$len$和。

在$parent\ tree$上造虚树，统计一下每个点子树中有多少个关键点记为$siz$。

考虑每个节点的贡献，它所有儿子两两之间会有$siz$之积的点数$LCA$是它，随便加一加乘一乘就好了。

![](\images\emotion\6.jpg)

（胡言乱语.jpg）

下面由我来展示一波~~高端~~错误：

建虚树时对$dfs$序排序，用的比较函数：

``` cpp
	inline bool cmp(int x,int y){return origin::seg[x]<origin::seg[y]?x:y;}
```

![](\images\emotion\7.jpg)

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

#define maxn 1000005
#define inf 0x3f3f3f3f

const long long mod = 23333333333333333ll;

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int n,m;
namespace Suffix_Automaton{
#define son(x,y) son[x][y]
	char s[maxn];
	int w[maxn],len[maxn],fa[maxn],son[maxn][26],last=1,cnt=1;
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
				len[sp]=len[p]+1,fa[sp]=fa[q],fa[q]=fa[ne]=sp;
				while(p&&son(p,c)==q)son(p,c)=sp,p=fa[p];
			}
		}
	}
	void init(){
		scanf("%s",s+1);
		for(register int i=n;i;--i)insert(s[i]-'a');
		w[0]=1;
		for(register int i=1;i<=n;++i)w[i]=son(w[i-1],s[n-i+1]-'a');
		for(register int i=1;i<=n>>1;++i)swap(w[i],w[n-i+1]);
	}
}
namespace origin{
	int seg[maxn],top[maxn],son[maxn],siz[maxn],deep[maxn],fa[maxn],h[maxn],num,all;
	struct edge{
		int pre,to;
	}e[maxn];
	inline void add(int from,int to){
		e[++num].pre=h[from],h[from]=num,e[num].to=to;
	}
	void dfs1(int node=1){
		siz[node]=1;
		int x;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			deep[x]=deep[node]+1,fa[x]=node,dfs1(x);
			siz[node]+=siz[x];
			if(siz[x]>siz[son[node]])son[node]=x;
		}
	}
	void dfs2(int node=1){
		seg[node]=++all;
		if(son[node]){
			top[son[node]]=top[node],dfs2(son[node]);
			int x;
			for(register int i=h[node];i;i=e[i].pre){
				x=e[i].to;
				if(x!=son[node])top[x]=x,dfs2(x);
			}
		}
	}
	int lca(int x,int y){
		while(top[x]!=top[y])deep[top[x]]<deep[top[y]]?y=fa[top[y]]:x=fa[top[x]];
		return deep[x]<deep[y]?x:y;
	}
}
namespace virt{
	long long ans;
	int siz[maxn],h[maxn],num;
	vector<int>p;
	bool vis[maxn];
	struct edge{
		int pre,to;
	}e[maxn];
	inline void add(int from,int to){
		if(origin::seg[from]>origin::seg[to])swap(from,to);
		e[++num].pre=h[from],h[from]=num,e[num].to=to;
	}
	inline bool cmp(int x,int y){return origin::seg[x]<origin::seg[y];}
	struct Monostack{
		int sta[maxn],top;
		void push(int x){sta[++top]=x;}
		void clear(){
			for(register int i=2;i<=top;++i)add(sta[i-1],sta[i]);
			top=0;
		}
		void check(int x){
			int l=origin::lca(x,sta[top]);
			h[x]=0;
			if(l!=sta[top]){
				while(origin::seg[l]<origin::seg[sta[top-1]])add(sta[top-1],sta[top]),--top;
				--top;
				if(l!=sta[top])h[l]=0,add(sta[top+1],l),push(l);
				else add(sta[top+1],l);
			}
			push(x);
		}
	}s;
	void build(){
		h[1]=0,s.push(1);
		for(vector<int>::iterator iter=p.begin();iter!=p.end();++iter)s.check(*iter);
		s.clear();
	}
	void dfs(int node=1){
		siz[node]=vis[node];
		int x;
		long long k=0;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			dfs(x),k+=1ll*siz[x]*siz[node],siz[node]+=siz[x];
			if(k>=mod)k%=mod;
		}
		ans+=k*Suffix_Automaton::len[node];	
		if(ans>=mod)ans%=mod;
	}
	void solve(){
		ans=num=0,p.clear();
		int t=read(),x;
		while(t--){
			if(vis[x=Suffix_Automaton::w[read()]])continue;
			p.push_back(x),vis[x]=1;
		}
		sort(p.begin(),p.end(),cmp);
		build(),dfs();
		for(vector<int>::iterator iter=p.begin();iter!=p.end();++iter)vis[*iter]=0;
		printf("%lld\n",ans);
		
	}
}
int main(){
	n=read(),m=read();
	Suffix_Automaton::init();
	for(register int i=2;i<=Suffix_Automaton::cnt;++i)origin::add(Suffix_Automaton::fa[i],i);
	origin::dfs1(),origin::dfs2();
	while(m--)virt::solve();
}

```

