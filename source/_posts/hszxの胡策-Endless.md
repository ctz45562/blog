---
title: hszxの胡策 Endless
date: 2020-06-08 13:54:19
categories: 题解
tags:
    - 字符串
    - 后缀数组
    - ST表
keywords: endless
description: 大概是最后一次写题解了吧。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@2.1.7/endless.jpg
mathjax: true
---

大概是最后一次写题解了吧。（并不是）

<!--more-->

![](/images/endless-1.png)

题目规定的形如$AA$的串自然可以想到[优秀的拆分](/2019/04/21/洛谷-P1117-NOI2016优秀的拆分/)，根据套路，枚举$A$串的长度$len$，每$len$的长度设置一个关键点，$AA$串一定会跨过关键点。

对于每个相邻关键点$i,j$，若$lcs(i,j)+lcp(i+1,j+1)\ge len$就可以产生$AA$串，且左右端点在$[i-lcs(i,j)+1,i+lcp(i+1,j+1)]$内移动，于是$\forall k\in[i-lcs(i,j)+1,i+lcp(i+1,j+1)]$，需连边$(k,k+len)$。

考虑这个操作：一个区间与另一个区间对应连边，容易想到[萌萌哒](https://www.luogu.com.cn/problem/P3295)。

用$f(i,k)$表示点集$[i,i+2^k-1]$，把每次连边拆成两次$ST$表上的连边。

最后从高层往底层推下去。若有边$(f(i,k),f(j,k))$，则必有边$(f(i,k-1),f(j,k-1)),(f(i+2^{k-1},k-1),f(i+2^{k-1},k-1))$。不过直接把边推到底层边数还是爆炸的，所以可以对每层做一遍最小生成树，把有用的边下放，可以保证每层的边数都是$O(n)$的。

枚举$AA$串长度是调和级数的$O(n\log n)$，$ST$表的下放是$O(n\log^2n)$的，总复杂度$O(n\log^2n)$，空间复杂度$O(n\log n)$。

为了卡时间卡空间，线段树替换$ST$表求$lcp$，归并排序合并边集，写垃圾桶回收废弃边，代码丑的一批：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <vector>

#define maxn 300005
#define inf 0x3f3f3f3f

using namespace std;

inline int read(){
	int x=0,y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
int lg[maxn],fa[maxn],s[maxn],pool[maxn*30],edg[maxn<<4],rec[maxn<<3],top,n,m,cnt,all,num;
struct edge{
	int x,y,l;
	bool operator < (const edge &X)const{return l<X.l;}
}e[maxn*40];
vector<int>ed[21];
struct Suffix_Array{
#define ls(x) x<<1
#define rs(x) x<<1|1
    int sa[maxn],rk[maxn],tp[maxn],tax[maxn],hei[maxn],mi[maxn<<2];
    void Rsort(){
        for(register int i=0;i<=m;++i)tax[i]=0;
        for(register int i=1;i<=n;++i)++tax[rk[i]];
        for(register int i=1;i<=m;++i)tax[i]+=tax[i-1];
        for(register int i=n;i;--i)sa[tax[rk[tp[i]]]--]=tp[i];
    }
    void Ssort(){
        for(register int i=1;i<=n;++i)rk[i]=s[i],tp[i]=i;
        m=n,Rsort();
        for(register int k=1,p=0;p<n;m=p,k<<=1){
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
            while(i+k<=n&&x+k<=n&&s[i+k]==s[x+k])++k;
            hei[rk[i]]=k;
        }
    }
	void build(int l,int r,int node){
		if(l==r){mi[node]=hei[l];return;}
		int mid=l+r>>1;
		build(l,mid,ls(node));
		build(mid+1,r,rs(node));
		mi[node]=min(mi[ls(node)],mi[rs(node)]);
	}
	int query(int L,int R,int l,int r,int node){
		if(L<=l&&R>=r)return mi[node];
		int mid=l+r>>1,ans=inf;
		if(L<=mid)ans=query(L,R,l,mid,ls(node));
		if(R>mid)ans=min(ans,query(L,R,mid+1,r,rs(node)));
		return ans;
	}
    inline int lcp(int i,int j){
        return query(min(rk[i],rk[j])+1,max(rk[i],rk[j]),1,n,1);
    }
	void clear(){
		for(register int i=1;i<=n;++i)tp[i]=rk[i]=0;
	}
    void init(){
        Ssort(),get_height(),build(1,n,1);
    }
}pre,suf;
inline int LCS(int x,int y){
    if(!x||!y)return 0;
    return pre.lcp(n-x+1,n-y+1);
}
inline int LCP(int x,int y){return suf.lcp(x,y);}
inline void add(int l,int r,int len,int w){
	int k=lg[r-l+1];
	e[++cnt]=(edge){l,l+len,w};
	ed[k].push_back(cnt);
	e[++cnt]=(edge){r-(1<<k)+1,r+len-(1<<k)+1,w};
	ed[k].push_back(cnt);
}
int find(int x){return fa[x]==x?x:fa[x]=find(fa[x]);}
inline int newn(){return top?pool[top--]:++cnt;}
void merge(vector<int>&b){
	all=0;
	int i=1,j=0;
	while(i<=num&&j<b.size()){
		if(e[rec[i]].l<e[b[j]].l)edg[++all]=rec[i++];
		else edg[++all]=b[j++];
	}
	while(i<=num)edg[++all]=rec[i++];
	while(j<b.size())edg[++all]=b[j++];
}
inline bool cmp(int x,int y){return e[x].l<e[y].l;}
int main(){
	int t=read();
	while(t--){
		suf.clear(),pre.clear();
		n=read(),cnt=top=0;
		for(register int i=1;i<=n;++i)s[i]=read();
		for(register int i=2;i<=n;++i)lg[i]=lg[i>>1]+1;
		suf.init();
		reverse(s+1,s+1+n),pre.init();
		for(register int len=1;len<=(n>>1);++len){
			int w=read();
			for(register int i=len<<1;i<=n;i+=len){
				int j=i-len,lcs=min(LCS(i,j),len),lcp=min(LCP(i+1,j+1),len-1);
				if(lcs+lcp>=len)add(j-lcs+1,j+lcp,len,w);
			}
		}
		vector<int>::iterator it;
		long long ans=0;
		for(register int j=lg[n];~j;--j){
			sort(ed[j].begin(),ed[j].end(),cmp),merge(ed[j]),ed[j].clear(),num=0;
			for(register int i=1;i<=n;++i)fa[i]=i;
			for(register int i=1;i<=all;++i){
				edge E=e[edg[i]];
				int u=find(E.x),v=find(E.y);
				if(u!=v){
					fa[u]=v;
					if(j){
						rec[++num]=edg[i];
						int p=newn();
						e[p]=(edge){E.x+(1<<j-1),E.y+(1<<j-1),E.l},rec[++num]=p;
					}
					else ans+=E.l;
				}
				else pool[++top]=edg[i];
			}
		}
		printf("%lld\n",ans);
	}
}
```