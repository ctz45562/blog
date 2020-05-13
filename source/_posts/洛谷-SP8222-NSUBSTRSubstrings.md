---
title: SPOJ8222 NSUBSTR - Substrings
date: 2019-04-17 07:24:07
categories: 题解
tags:
	- 字符串
	- 后缀数组
	- 单调栈
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/nsubstr-substrings.jpg
keywords: Substrings
description: SAM​是啥？来一发朴素的SA​解法。
---

[传送门](https://www.luogu.org/problemnew/show/SP8222)

$SAM$是啥？来一发朴素的$SA$解法。

<!--more-->

先把$SA$建出来。如果某两个子串$S_1,S_2$相同的话，它们所属的后缀的$LCP$要大于等于$len(S_1)$。

$LCP$是$\min\{height[k]\}(i<k\le j)$，然后就变成了对每个长度$L$，求出最长的一段区间$[l,r]$使$height[i]\ge L(i\in[l,r])$。单调栈扫两遍就行了。

代码：

``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 250005
#define inf 0x3f3f3f3f
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
int sa[maxn],rk[maxn],hei[maxn],tp[maxn],tax[maxn],ll[maxn],rr[maxn],ans[maxn],n,m;
char s[maxn];
struct MonoStack{
	int sta[maxn],top;
	inline void push(int x){
		while(top>1&&hei[sta[top]]>=hei[x])--top;
		sta[++top]=x;
	}
	inline int back(){
		return sta[top-1];
	}
	inline void clear(){
		top=0;
	}
}st;//单调栈
void Rsort(){
	for(register int i=0;i<=m;++i)tax[i]=0;
	for(register int i=1;i<=n;++i)++tax[rk[i]];
	for(register int i=1;i<=m;++i)tax[i]+=tax[i-1];
	for(register int i=n;i;--i)sa[tax[rk[tp[i]]]--]=tp[i];
}
void Ssort(){
	for(register int i=1;i<=n;++i)rk[i]=s[i],tp[i]=i;
	m=225,Rsort();
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
void solve(){
	st.push(0);
	for(register int i=1;i<=n;++i)
		st.push(i),ll[i]=st.back()+1;
	st.clear(),st.push(n+1);
	for(register int i=n;i;--i)
		st.push(i),rr[i]=st.back()-1;
	for(register int i=1;i<=n;++i)
		ans[hei[i]]=max(ans[hei[i]],rr[i]-ll[i]+2);//先只更新当前答案 ，比hei[i]小的下面做一个后缀最大值取到
	ans[n+1]=1;//答案最少为1
	for(register int i=n;i;--i)
		ans[i]=max(ans[i],ans[i+1]);
	for(register int i=1;i<=n;++i)
		printf("%d\n",ans[i]);
}
int main(){
	scanf("%s",s+1),n=strlen(s+1);
	Ssort(),get_height(),solve();
}

```

