---
title: '洛谷 P4595 [COCI2011-2012#5]POPLOCAVANJE'
date: 2019-07-09 12:30:56
categories: 题解
tags:
	- 字符串
	- 后缀自动机
description: 省队集训=自闭+水题+颓废
keywords: POPLOCAVANJE
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/poplocavanje.jpg
mathjax: true
---

[传送门](https://www.luogu.org/problemnew/show/P4595)

省队集训=自闭+水题+颓废

<!--more-->

卡空间的$AC$自动机板子题。不会什么~~奇技淫巧~~于是选择了线性空间的$SAM$。

显然要对母串造$SAM$，把模式串在$SAM$跑一遍。如果一个模式串在节点$i$停下，它就能和所有属于$endpos_i$的位置匹配。

维护一个$ma_i$表示在节点$i$停下的最长的模式串长度。每个节点给区间$[\max\{pos-ma_i+1,1\},pos] (pos\in endpos_i)$加$1$。某个位置大于$0$说明可以修补，差分即可。

当然我们并不需要对每个节点都操作。一个点的$endpos$都来自于$parent\ tree$的子树，把$ma$推下去在叶子结点操作即可。

一开始开的$map$，不过额外空间开销不小还是$M$了，于是~~厚颜无耻地~~开数组靠动态内存$A$了。要没有动态内存的话。。。也许要手写平衡树？

代码：
``` cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <map>

#define maxn 600005
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
int son[maxn][26],fa[maxn],len[maxn],ma[maxn],sum[maxn],pos[maxn],cur[maxn],tax[maxn],all,num,last=1,cnt=1;
char s[maxn];
inline void modify(int l,int r){
    ++sum[l],--sum[r+1];
}
void insert(int c){
    int p=last,ne=last=++cnt;
    pos[ne]=len[ne]=len[p]+1;
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
int main(){
    int n=read(),m,t,node,ans=0;
    scanf("%s",s+1);
    for(register int i=1;i<=n;++i)insert(s[i]-'a');
    for(register int i=1;i<=cnt;++i)++tax[len[i]];
    for(register int i=1;i<=n;++i)tax[i]+=tax[i-1];
    for(register int i=1;i<=cnt;++i)cur[tax[len[i]]--]=i;
    t=read();
    while(t--){
        scanf("%s",s+1),m=strlen(s+1),node=1;
        for(register int i=1;i<=m&&node;++i)node=son(node,s[i]-'a');
        if(node)ma[node]=max(ma[node],m);
    }
    for(register int i=2;i<=cnt;++i){
        t=cur[i];
        ma[t]=max(ma[t],ma[fa[t]]);
        if(ma[t]&&pos[t])modify(max(pos[t]-ma[t]+1,1),pos[t]);
    }
    for(register int i=1;i<=n;++i){
        sum[i]+=sum[i-1];
        if(!sum[i])++ans;
    }
    printf("%d\n",ans);
}
```
