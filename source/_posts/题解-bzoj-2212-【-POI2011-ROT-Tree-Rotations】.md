---
title: 'bzoj 2212[POI2011]ROT-Tree Rotations'
date: 2019-03-07 21:48:14
categories: 题解
tags:
	- 数据结构
	- 线段树
	- 贪心
	- 线段树合并
mathjax: true
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/rot-treerotations.jpg
keywords: ROT-Tree Rotations
description: 前序遍历叶子节点，可以理解为把叶子节点从左到右输出。
---

[传送门](https://www.luogu.org/problemnew/show/P3521)

[能看得懂题面的传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=2212)

前序遍历叶子节点，可以理解为把叶子节点从左到右输出。

<!--more-->

那么首先考虑一个性质：**交换某个节点的左右儿子，仅会对这两棵子树之间的逆序对造成影响，而对某棵子树内部、它祖宗十八代之间的逆序对没有影响。**

某个节点的左子树和右子树有哪些数换完之后还是有哪些数，交换后相对位置变化的只有这两棵子树，所以仅对这两棵子树有影响。

这样就有一个贪心的思路：如果某个节点交换左右儿子更优则交换。

怎么叫更优？交换后左、右子树之间逆序对小于交换前的就算更优。（注意这里的逆序对就针对于这两棵子树之间的值，不算上子树内部产生的逆序对）

现在问题转化成了：计算每个节点左、右子树之间的逆序对数。首先可以想到合并。其实平衡树启发式合并和线段树合并都珂以，不过平衡树$O(nlog^2n)$太慢了，这里选择线段树合并。

对树进行$dfs$，使用动态开点权值线段树，从底部将叶子节点的权值一步步合并上来，在任意节点就能得到其左右儿子的线段树。对于求两棵子树间的逆序对，珂以在合并时求出。放图举个栗子：

![](\images\ROT-1.png)

比如现在合并到了蓝色节点，就让计数变量加上**左儿子线段树中该节点的右儿子的值 x 右儿子线段树上该节点的左儿子的值**，也就是上图中红色节点的值乘绿色节点的值。然后在递归的每个节点都执行这个操作。

这个操作可以理解为：不考虑某个节点内部的逆序对情况，**红色节点中的每个值都能与绿色节点中的每个值产生逆序对**，于是有两值相乘为答案。至于节点内部就可以继续递归求出。

交换后的逆序对数：记$sizl$为左子树权值（叶子节点）的个数，$sizr$为右子树权值的个数，交换前有$x$对逆序对。则交换后就有$sizl\*sizr-x$对逆序对。

这个容易理解：共有$sizl\*sizr$种组合，每个之前不符合逆序对的组合，交换后就符合了。同理之前符合的交换后就不符合了。

答案的统计：珂以$dfs$完之后直接前序遍历用归并排序或树状数组求出。但是在$dfs$中已经求出每个节点左右儿子间逆序对数，就可以把这个值加起来作为答案，效率高很多。

还有注意开$long\ long$

代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>

#define maxn 200005
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
template<typename T>
inline T read(){
	T x=0;
	int y=0;
	char ch=getchar();
	while(ch<'0'||ch>'9'){if(ch=='-')y=1;ch=getchar();}
	while(ch>='0'&&ch<='9')x=(x<<1)+(x<<3)+(ch^48),ch=getchar();
	return y?-x:x;
}
struct Tree{
	int ls,rs,root,d;
    //左儿子、右儿子、线段树的根、权值
}t[maxn<<1];
//节点数要开两倍
int num=1,n;
long long ans,all;
//all是最终答案，ans是中间统计左右子树间逆序对的计数器
struct Segment_Tree{
	int dat[maxn<<5],ls[maxn<<5],rs[maxn<<5],cnt;
#define ls(x) ls[x]
#define rs(x) rs[x]
	void add(int poi,int l,int r,int &node){
		node=++cnt;
		dat[node]=1;
		if(l==r)return;
		int mid=l+r>>1;
		if(poi<=mid)add(poi,l,mid,ls(node));
		else add(poi,mid+1,r,rs(node));
	}
	int merge(int l,int r,int x,int y){
		if(!x||!y)return x|y;//有空节点返回另一个
		dat[x]+=dat[y];//合并两个节点信息
		if(l==r)return x;//递归到底层返回
		int mid=l+r>>1;
		ans+=1ll*dat[rs(x)]*dat[ls(y)];
        //这里x为左儿子的节点，y为右儿子的节点，就有上面提到的统计方法
		ls(x)=merge(l,mid,ls(x),ls(y));
		rs(x)=merge(mid+1,r,rs(x),rs(y));
		return x;
	}//线段树合并
}st;
void dfs(int node=1){
	if(!t[node].ls)return;
	dfs(t[node].ls);
	dfs(t[node].rs);
    //先处理左右儿子，得到左右儿子的情况
	ans=0;
    //计数器清零
	long long siz=1ll*st.dat[t[t[node].ls].root]*st.dat[t[t[node].rs].root];
	t[node].root=st.merge(1,n,t[t[node].ls].root,t[t[node].rs].root);
	if(ans>siz-ans)swap(t[node].ls,t[node].rs),all+=siz-ans;
	else all+=ans;
    //直接用中间过程求的值统计最终答案
}
void Get(int node=1){
	t[node].d=read();
	if(!t[node].d)Get(t[node].ls=++num),Get(t[node].rs=++num);
	else st.add(t[node].d,1,n,t[node].root);
}//递归读入
int main(){
	n=read();
	Get();
	dfs();
	printf("%lld\n",all);
}

```

