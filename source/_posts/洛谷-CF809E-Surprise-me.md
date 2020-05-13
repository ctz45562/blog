---
title: Codeforces809E Surprise me!
date: 2019-08-05 15:52:10
categories: 题解
tags:
	- 数论
	- 莫比乌斯反演
	- 虚树
keywords: Surprise me
description: wdnmd这题做起来刺激
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/surpriseme.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problem/CF809E)

$\text{wdnmd}$这题做起来刺激{% emoji_coda xiaodianshi/haixiu %}

<!--more-->

转换一下$a_i$的定义：权值为$i$的点的编号。

这样求的答案就成了$\sum\limits_{i=1}^n\sum\limits_{j=1}^n\varphi(i\times j)dis(a_i,a_j)$

反演还是很套路的：

$=\sum\limits_{i=1}^n\sum\limits_{j=1}^n\dfrac{\varphi(i)\varphi(j)gcd(i,j)}{\varphi(gcd(i,j))}dis(a_i,a_j)$

$=\sum\limits_{d=1}^n\dfrac{d}{\varphi(d)}\sum\limits_{i=1}^n\varphi(i)\sum\limits_{j=1}^n\varphi(j)[gcd(i,j)=d]dis(a_i,a_j)$

$=\sum\limits_{d=1}^n\dfrac{d}{\varphi(d)}\sum\limits_{i=1}^{\lfloor\frac{n}{d}\rfloor}\varphi(id)\sum\limits_{j=1}^{\lfloor\frac{n}{d}\rfloor}\varphi(jd)dis(a_{id},a_{jd})\sum\limits_{a|gcd(i,j)}\mu(a)$

$=\sum\limits_{d=1}^n\dfrac{d}{\varphi(d)}\sum\limits_{a=1}^{\lfloor\frac{n}{d}\rfloor}\mu(a)\sum\limits_{i=1}^{\lfloor\frac{n}{ad}\rfloor}\varphi(iad)\sum\limits_{j=1}^{\lfloor\frac{n}{ad}\rfloor}\varphi(jad)dis(a_{iad},a_{jad})$

令$T=ad$，枚举$T$：

$=\sum\limits_{d=1}^n\dfrac{d}{\varphi(d)}\sum\limits_{T=1}^n[d|T]\mu(\dfrac{T}{d})\sum\limits_{i=1}^{\lfloor\frac{n}{T}\rfloor}\varphi(iT)\sum\limits_{j=1}^{\lfloor\frac{n}{T}\rfloor}\varphi(jT)dis(a_{iT},a_{jT})$

$=\sum\limits_{T=1}^n\sum\limits_{i=1}^{\lfloor\frac{n}{T}\rfloor}\varphi(iT)\sum\limits_{j=1}^{\lfloor\frac{n}{T}\rfloor}\varphi(jT)dis(a_{iT},a_{jT})\sum\limits_{d|T}\mu(\dfrac{T}{d})\dfrac{d}{\varphi(d)}$

最后面$\sum\limits_{d|T}$的一坨是个卷积形式，可以线筛，不过$O(n\log n)$枚举倍数预处理就够了。

看中间那一坨，考虑它的含义，其实就是把编号为$a_{iT}$的点拿出来，点权为$\varphi(iT)$，求它们两两之间$value(i)\times value(j)\times dis(i,j)$之和。

而这个式子是个调和级数，总点数是$O(n\log n)$的，大力对点集$\{a_{iT}|i\in \mathbb{Z}\}$上虚树。

记$siz(i)$为点$i$的子树权值和，考虑每条边$edge(i,j)$的贡献，为$length(edge(i,j))\times siz(j)\times(siz(root)-siz(j))$。

于是就能$O(n\log^2n)$解决辣。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

#define maxn 200005
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
int deep[maxn],seg[maxn],a[maxn],all;
vector<int>p;
bool vis[maxn];
namespace origin{
	int top[maxn],fa[maxn],son[maxn],siz[maxn],h[maxn],num;
	struct edge{
		int pre,to;
	}e[maxn<<1];
	inline void add(int from,int to){
		e[++num].pre=h[from],h[from]=num,e[num].to=to;
	}
	void dfs1(int node=1){
		siz[node]=1;
		int x;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			if(siz[x])continue;
			fa[x]=node,deep[x]=deep[node]+1,dfs1(x),siz[node]+=siz[x];
			if(siz[x]>siz[son[node]])son[node]=x;
		}
	}
	void dfs2(int node=1){
		seg[node]=++all;
		if(son[node]){
			int x=son[node];
			top[x]=top[node],dfs2(x);
			for(register int i=h[node];i;i=e[i].pre){
				x=e[i].to;
				if(!seg[x])top[x]=x,dfs2(x);
			}
		}
	}
	int lca(int x,int y){
		while(top[x]!=top[y])deep[top[x]]<deep[top[y]]?y=fa[top[y]]:x=fa[top[x]];
		return deep[x]<deep[y]?x:y;
	}
}
namespace virt{
	int siz[maxn],h[maxn],num,ans;
	struct edge{
		int pre,to,l;
	}e[maxn<<1];
	inline void add(int from,int to){
		int l=abs(deep[from]-deep[to]);
		e[++num].pre=h[from],h[from]=num,e[num].to=to,e[num].l=l;
		e[++num].pre=h[to],h[to]=num,e[num].to=from,e[num].l=l;
	}
	bool cmp(int x,int y){return seg[x]<seg[y];}
	struct Monostack{
		int sta[maxn],top;
		void push(int x){sta[++top]=x;}
		void clear(){
			for(register int i=2;i<=top;++i)add(sta[i],sta[i-1]);
			top=0;
		}
		void check(int x){
			if(x==1)return;
			int l=origin::lca(x,sta[top]);
			h[x]=0;
			if(l!=sta[top]){
				while(seg[l]<seg[sta[top-1]])add(sta[top],sta[top-1]),--top;
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
	void dfs(int node=1,int fa=0){
		if(!vis[node])siz[node]=0;
		int x;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			if(x!=fa)dfs(x,node),(siz[node]+=siz[x])%=mod;
		}
	}
	void calc(int node=1,int fa=0){
		int x;
		for(register int i=h[node];i;i=e[i].pre){
			x=e[i].to;
			if(x!=fa)calc(x,node),(ans+=1ll*e[i].l*siz[x]%mod*(siz[1]-siz[x])%mod)%=mod;
		}
	}
	int solve(){
		sort(p.begin(),p.end(),cmp);
		num=0,ans=0,build(),dfs(),calc();
		for(vector<int>::iterator iter=p.begin();iter!=p.end();++iter)vis[*iter]=siz[*iter]=0;
		return ans;
	}
}
namespace mobius{
	int phi[maxn],mu[maxn],f[maxn],prime[maxn>>2],inv[maxn],cnt;
	bool is[maxn];
	void solve(int n){
		int ans=0;
		is[1]=phi[1]=mu[1]=inv[1]=1;
		for(register int i=2;i<=n;++i){
			inv[i]=1ll*(mod-mod/i)*inv[mod%i]%mod;
			if(!is[i])prime[++cnt]=i,phi[i]=i-1,mu[i]=-1;
			for(register int j=1;j<=cnt&&i*prime[j]<=n;++j){
				is[i*prime[j]]=1;
				if(i%prime[j]==0){
					mu[i*prime[j]]=0,phi[i*prime[j]]=phi[i]*prime[j];
					break;	
				}
				phi[i*prime[j]]=phi[i]*(prime[j]-1),mu[i*prime[j]]=-mu[i];
			}
		}
		for(register int i=1;i<=n;++i)
			for(register int j=i;j<=n;j+=i)
				(f[j]+=1ll*mu[j/i]*i*inv[phi[i]]%mod)%=mod;
		for(register int i=1;i<=n;++i){
			p.clear();
			for(register int j=i;j<=n;j+=i)
				p.push_back(a[j]),vis[a[j]]=1,virt::siz[a[j]]=phi[j];
			(ans+=1ll*virt::solve()*f[i]%mod)%=mod;
		}
		ans=1ll*(ans<<1)*inv[n]%mod*inv[n-1]%mod;
		printf("%d\n",(ans+mod)%mod);
	}
}
int main(){
	using namespace origin;
	int n=read(),x,y;
	for(register int i=1;i<=n;++i)a[read()]=i;
	for(register int i=1;i<n;++i)x=read(),y=read(),add(x,y),add(y,x);
	dfs1(),dfs2();
	mobius::solve(n);
}

```

