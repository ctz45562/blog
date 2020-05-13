---
title: 降智の应急处理笔记
date: 2019-09-27 11:03:04
categories: 学习笔记
tags:
	- 动态规划
	- 图论
	- 状态压缩
	- 数位DP
	- 贪心
keywords: 动态规划
description: 智商不够用，刷DP来凑。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/jzdyjclbj.jpg
mathjax: true
---

智商不够用，刷$DP$来凑。

<!--more-->

# 前言

和我去年同一时间的状态一样开始刷$DP$，预示了我$CSP$凉凉的结局。

题单来源于[asuldb](https://www.cnblogs.com/asuldb/)。以前做过的就不整理了，~~还混有一些我绝不会碰的题和抄不动题解自闭了的题也不写了~~。

至于后面没有出现的题，那就是刷不动了。{% emoji_coda quyin/watermaleon %}

$update$:一些我被降智的题也被收录了。 

---

# DP的精髓——asuldb


## [[GDOI2014]拯救莫莉斯](https://www.luogu.org/problem/P3888)

$n\times m\le 50$，$\min\{n,m\}\le 7$，考虑状压。（以下$n>m$）

设$f(i,j,k)$为前$i$行，第$i-1$行状态为$j$，第$i$行状态为$k$的最小花费。

先$O(m2^{3m})$处理出每个状态$j,k$能向那些状态转移，且$k$的油库满足。

然后直接$O(n2^{3m})$转移即可，还要输出方案数随便搞一搞就行了。

这样第一行和最后一行可能不满足，预处理和取答案时判断一下就好了。

## [[APIO2010]巡逻](https://www.luogu.org/problem/P3629)

$k=1$显然是在直径两端点连边。

$k=2$时，若连的两端点形成的环与第一条边形成的环有交，交集的边就要走两次，也就是抵消了第一条边的贡献。

把直径上的边权值改为$-1$再求一遍直径即可。

## [[AHOI2008]逆序对](https://www.luogu.org/problem/P4280)

[题解](/2019/09/26/洛谷-P4280-AHOI2008-逆序对/)

奇妙的性质：填进去的数是单调不降的。

然后就$O(nk)$随便$DP$了。

## [[NOI2009]管道取珠](https://www.luogu.org/problem/P1758)

考虑$\sum a_i^2$的含义：有两套管道，两套管道取出的珠子序列相同的方案数。

设$f(i,j,k,l)$为第一套上管道取了$i$个，下管道取了$j$个，第二套上管道取了$k$个，下管道取了$l$个，所得序列相同的方案数。

$i+j=k+l$可以消掉一维。

刷表转移：$f(i,j,k)\rightarrow \begin{cases}f(i+1,j,k+1)&a_{i+1}=a_{k+1}\\f(i+1,j,k)&a_{i+1}=b_{i+j-k+1}\\f(i,j+1,k+1)&b_{j+1}=a_{k+1}\\f(i,j+1,k)&b_{j+1}=b_{i+j-k+1}\end{cases}$

## [[CQOI2018]解锁屏幕](https://www.luogu.org/problem/P4460)

$n\le 20$直接状压。

搞个状态数组$s[i][j]$，将点$i,j$间连线，经过的点在$s[i][j]$对应位上是$1$。

设$f(i,j)$表示状态为$j$（$1$表示已使用过），最后一个点为$i$的方案数，结合$s$数组随便转移即可。

## [[APIO2007]动物园](https://www.luogu.org/problem/P3622)

状压。设$f(i,j)$为前$i$个围栏，第$i\sim i+4$个围栏的状态为$j$（$1$表示留下）的最多高兴人数。

$O(n2^5)$预处理数组$cnt[i][j]$表示仅考虑第$i$个位置的人，$i\sim i+4$的状态为$j$的高兴人数。

由于是个环，枚举第一个位置的状态依次$DP$，刷表转移$f(i,j)+cnt[i+1][j>>1]\rightarrow f(i+1,j>>1),f(i,j)+cnt[i+1][j>>1|16]\rightarrow f(i+1,j>>1|16)$

取答案时只取与当前枚举的状态符合的答案。复杂度$O(2^{10}n)$。

## [[HAOI2015]树上染色](https://www.luogu.org/problem/P3177)

考虑每条边的贡献。设$f(i,j)$为在子树$i$中钦定$j$个黑点，**里面的边对整棵树的最大贡献**。

转移时考虑连向子节点的边的贡献，即两侧黑点数的乘积加上白点数的乘积。

$f(i,j)=\max\limits_{x\in son(i)}\{f(i,j-k)+f(x,k)+edge(i,x).l\times[k\times(K-k)+(size_x-k)\times(n-K-size_x+k)]\}$

## [[SDOI2017]序列计数](https://www.luogu.org/problem/P3702)

一开始我是想设$f(i,j,0/1)$表示前$i$个数、和模$p$等于$j$、是否出现过质数的方案数。

后来发现都搞的容斥。。。

设$f(i,j)$为前$i$个数、和模$p$等于$j$的方案数，枚举数字转移。两遍$DP$，第一遍没有限制，第二遍只枚举合数。用第一遍的减去第二遍的就是答案。

再来个矩乘优化就行了。

## [[SDOI2008]山贼集团](https://www.luogu.org/problem/P2465)

$p\le 12$显然状压。

设$f(i,j)$表示子树$i$状态为$j$（$1$表示在**子树**$i$建立分部）的最大收益。

转移类似树形背包：$f(i,j)=\max\limits_{x\in son(i),(k)_2\subseteq (j)_2}\{f(x,k)+f(i,k\ xor\ j)\}$

其中$k\ xor\ j$相当于取补集。

由于要枚举子集，复杂度是$O(n3^p)$的。

## [[SDOI2009]Bill的挑战](https://www.luogu.org/problem/P2167)

状压。第一眼以为要上$AC$自动机，发现串长都相等直接暴力$DP$。

$O(26|S|)$预处理一个状态数组$g[i][j]$，二进制下第$x$位为$1$表示$S_x$第$i$位是字母$j$或$?$。

设$f(i,j)$为填到第$i$位，当前还能匹配的串为状态$j$。

枚举字母刷表转移： $f(i,j)\rightarrow f(i+1,j\&g[i+1]['a'\sim'z'])$

复杂度$O(T26|S|2^n)$。

## [[POI2015]WIL-Wilcze doły](https://www.luogu.org/problem/P3594)

这题和$DP$没啥关系。

不过我还是十分睿智地写了个二分，$O(2000000\log 2000000)$才$4e7$不慌。

记$sum$为前缀和，$ma_i$为$sum_{i+d}-sum_{i-1}$。

某个区间的最小权值就是区间和减去长度为$d$的最大子段和，即$sum_r-sum_{l-1}-\max\limits_{i=l}^{r-d+1}\{ma_i\}$。

然后尺取就好了啊，中间要维护一个左右端点单调不降的区间$\max$，上单调队列。

当然如果和我一样$zz$非要写二分我也不拦你。

## [[USACO09DEC]牛收费路径Cow Toll Paths](https://www.luogu.org/problem/P2966)

非要说和$DP$有啥关系的话，大概是$floyd$吧。

直接对点权排序跑$floyd$就完了。

## [[USACO12FEB]附近的牛Nearby Cows](https://www.luogu.org/problem/P3047)

题单逐渐水了起来。。。

由于$k\le 20$，直接算出$f(i,j)$表示$i$子树中与$i$距离为$j$的点权和。

然后暴力跳$k$层父亲算上祖先的贡献，还要容斥减掉同一棵子树的点。

## [[USACO13NOV]没有找零No Change](https://www.luogu.org/problem/P3092)

状压。

设$f(i)$为状态为$i$（$1$表示已用过该硬币）能买的最多的物品数。

枚举下一个用的硬币，二分出最大能买到的位置，刷表转移。复杂度$O(k2^k\log n)$。

答案在满足$f(i)=n$的$i$里取即可。

## [[SCOI2009]粉刷匠](https://www.luogu.org/problem/P4158)

一开始以为可以随便刷，是个神仙题。

后来发现只能横向刷，瞬间沦为$zz$题。

对每行处理出一个$g(i,j)$，表示该行对前$i$个格子刷$j$次最多的正确格子数。

转移就枚举上次刷的位置和刷的颜色：$g(i,j)=\max\limits_{k=0}^{i-1}\{g(k,j-1)+\max\{sum_i-sum_k,i-k-sum_i+sum_k\}\}$（$sum_i$为前$i$个格子中蓝色格子数）

把每行看作一组，枚举刷的次数，$g(m,i)$看作若干件物品，直接做个分组背包即可。

## [[HAOI2008]木棍分割](https://www.luogu.org/problem/P2511)

就是跳石头统计方案数。

$O(n\log n)$预处理出每个点最靠左的端点$le_i$满足$sum_i-sum_{le_i}\le ans$。

设$f(i,j)$为跳了$j$步到点$i$的方案数。

普及难度的转移：$f(i,j)=\sum\limits_{k=le_i}^{i-1}f(k,j-1)$

$O(nm)$的空间开不下。然后思维僵化了，$i$的取值与前面的都有关系滚不动咋办啊。

第一维滚不动滚第二维啊。

再来个前缀和优化就没了。

## [[TJOI2017]城市](https://www.luogu.org/problem/P3761)

[水の题解](/2019/10/09/洛谷-P3761-TJOI2017-城市/)

## [简单题](https://darkbzoj.tk/problem/3687)

设$f(i)$为所有子集和中，$i$是否出现了奇数次。

实际上这就是个完全背包变形，`bitset`优化即可。

最后答案把所有$f(i)=1$的$i$异或起来。

复杂度$O(n\sum a_i/32)$

## [Codeforces1025D Recovering BST](https://www.luogu.org/problem/CF1025D)

先$O(n^2\log a_i)$把边处理出来。

容易想到设$f(i,j,k)$为取$[j,k]$的点能否构成以$i$为根的$BST$。

然而时空都受不了。

重新搞状态：设$l(i,j)$为取$[i,j]$的点能否构成以$i$为根的$BST$，$r(i,j)$为取$[i,j]$的点能否构成以$j$为根的$BST$。

做个区间$DP$。若存在$l(1,i)\&r(i,n)=1$则可行。

## [[SNOI2017]英雄联盟](https://www.luogu.org/problem/P5365)

正当逐渐走向自闭时，眼前一晃而过一抹绿色。{% emoji_coda 2233/chijing %}

设$f(i,j)$为前$i$个英雄，用$j\ QB$能买出的最大展示策略。

这不就一分组背包吗。

$f(i,j)=\max\limits_{k=1}^{K_i}\{f(i-1,j-k\times C_i)\times k\}$

算一下$n$最大才$120$，背包容量也不超过$300000$。滚动数组优化一波就水过去了~

## [[SDOI2016]储能表](https://www.luogu.org/problem/P4067)

[题解](/2019/10/10/洛谷-P4067-SDOI2016-储能表/)

## [[CTSC2017]吉夫特](https://www.luogu.org/problem/P3773)

[题解](/2019/10/10/洛谷-P3773-CTSC2017-吉夫特/)

## [[JSOI2018]潜入行动](https://www.luogu.org/problem/P4516)

[题解](/2019/10/11/洛谷-P4516-JSOI2018-潜入行动/)

## [[SDOI2018]荣誉称号](https://www.luogu.org/problem/P4620)

[题解](/2019/10/12/洛谷-P4620-SDOI2018-荣誉称号/)

## [[CQOI2007]涂色](https://www.luogu.org/problem/P4170)

我是不是要重学区间$DP$了{% emoji_coda quyin/cry %}

设$f(i,j)$为涂完区间$[i,j]$的最少次数。

初始$f(i,i)=1$。

讨论转移：

- 若$a_i=a_j$，可以涂$a_i$的时候一块把$a_j$涂了，或者反过来，即$f(i,j)=\min\{f(i+1,j),f(i,j-1)\}$
- 若$a_i\neq a_j$，枚举断点合并，$f(i,j)=\min\limits_{k=i}^{j-1}\{f(i,k)+f(k+1,j)\}$

## [Alice in linear land](https://www.luogu.org/problem/AT2401)

神仙题。{% emoji_coda quyin/look %}

题意：

> 数轴原点上有一个机器人，终点为$D$。有$n$条命令，每条命令为$d_i$，机器人会向终点移动$d_i$距离，若移动后距离变大则不移动。给定$m$次询问，允许改变$d_{q_i}$为任意整数，若存在方案使机器人不能走到终点输出$YES$，否则输出$NO$。

先处理出$pos_i$，表示前$i$条命令后与终点的距离。

考虑一个朴素的$DP$，设$g(i,j)$为当前与终点距离为$j$，仅后$i$条指令能否到达终点。

转移：$g(i,j)=\begin{cases}g(i+1,j)&d_i\ge 2j\\g(i+1,d_i-j)&j<d_i<2j\\g(i+1,j-d_i)&d_i\le j\end{cases}$

修改$d_i$时，可视为改变到终点的距离，取值范围$[0,pos_{i-1}]$。若$g(i+1,[0,pos_{i-1}])$存在$0$的话，就把距离改过去，就不能走到终点了。

发现我们只需求出满足$g(i,j)=0$的最小的$j$，设$f(i)$为该值。

类比$g$转移：

$f(i)=\min\begin{cases}f(i+1)&d_i\ge2f(i+1)\\d_i-f(i+1)&d_i>2f(i+1)\\f(i+1)+d_i\end{cases}$

初值$f(n+1)=1$，因为任意$g(i,0)=1$。

询问时判断$pos_{q_i-1}$与$f(q_i+1)$的大小即可。

## [[JSOI2016]最佳团体](https://www.luogu.org/problem/P4322)

一看$\dfrac{\sum p_i}{\sum s_i}$这样的式子就跑不掉$01$分数规划。

二分答案，问题转化为判定是否存在方案选$k$个人，使$\sum p_i-\sum mid\times s_i\ge 0$。

然后就是树形背包了。

话说$O(nk\log)$能跑过去真是神奇。{% emoji_coda xiaodianshi/xiao %}

---

#  さようなら ，IQ さん 

## [Codeforces55D Beautiful numbers](https://www.luogu.org/problem/CF55D)

数位$DP$。模$2520$的余数肯定是要记的。

然后钻到状压每个数字是否出现的牛角尖里出不来了{% emoji_coda xiaodianshi/fachou %}。

实际上从$1\sim 9$选若干个数，它们的$lcm$情况不到$50$种，把这个作为状态就没了。


## [Codeforces815C Karen and Supermarket](https://www.luogu.org/problem/CF815C)

硬核可怜题{% emoji_coda 2233/han %}。

显然是个树形背包，但这个依赖关系是对优惠券来说的。

尝试构造这棵树，失败，告辞。

实际上再加一维$0/1$表示该点是否用优惠券就行了。。。{% emoji_coda luotianyi/无言以对 %}

## [Codeforces1114D Flood Fill](https://www.luogu.org/problem/CF1114D)

区间$DP$再起不能。

设$f(i,j)$为使区间$[i,j]$同色的最小次数。

- 若$i,j$同色，可以把$[i+1,j-1]$涂好后再变成$i$的颜色：$f(i,j)=f(i+1,j-1)+1$
- 若$i,j$不同色，就从$[i+1,j]$和$[i,j-1]$里选代价最小的转移：$f(i,j)=\min\{f(i,j-1),f(i+1,j)\}+1$

## [Codeforces922E Birds](https://www.luogu.org/problem/CF922E)

看数据范围状态显然与$n$和$\sum c_i$有关。

设$f(i,j)$为到了第$i$棵树，有$j$只鸟的最大魔法值。

由于$B$是固定的，魔法上限也就确定了。直接枚举召唤几只鸟转移。

复杂度不对。然后优化，优化，优化。。。咋优化啊？

这$TM$不就是个多重背包吗！二进制优化啊！{% emoji_coda 2233/nu %}

有一点要注意的是不能直接把每棵树拆成若干件物品做$01$背包，只有当跨了一棵树才能魔法值$+X$。对每棵树单独背包，拆分前先把所有$f(i,j)$ 统一$+X$。

## [Codeforces983B XOR-pyramid](https://www.luogu.org/problem/CF983B)

$n\le 5000$考虑$O(n^2)$预处理。

列个更美观的表：

```
1: 1 2 4 8
2: 3 6 12
3: 5 10
4: 15
```

设$f(i,j)$为$a$数组经过$i$轮变换，第$j$位上的数字是多少。

观察得到转移：$f(i,j)=f(i-1,j)xorf(i-1.j+1)$

再观察可知：$f(i,j)$表示了$j$为左端点，长度为$i$的区间权值。

设$g(i,j)$为左端点大于等于$j$，长度不大于$i$的最大区间权值。

$g(i,j)=\max\{f(i,j),f(i-1,j+1),f(i-1,j)\}$

## [[SHOI2008]循环的债务](https://www.luogu.com.cn/problem/P4026)

先搞个宏伟的$DP$数组：

$f(i,j,k,a100,a50,a20,a10,a5,a1,b100,b50,b20,b10,b5,b1,c100,c50,c20,c10,c5,c1)$

慢慢优化，优化不动，自闭了。{% emoji_coda quyin/fue %}

每种钞票是独立的，所以设$f(i,j,k)$为前$i$种钞票，$Alice$有$j$元，$Bob$有$k$元。钱的总和是定值所以把$Ciyang$压没了。~~(暝阳·梅勒)~~

枚举交换完第$i$种钞票后$Alice,Bob$分别有多少张$i$钞票，没了。

## [ATcoder3870 Reversed LCS](https://www.luogu.org/problem/AT3870)

我又双叒叕被区间$DP$虐了。

显然与反串的$LCS$是最长回文子序列长度。

设$f(i,j,k)$为区间$[i,j]$修改$k$个字符的最大值。

转移：

- 啥都不干：$f(i,j,k)=\max\{f(i+1,j,k),f(i,j-1,k)\}$
- $s_i$与$s_j$相同，回文子序列延长：$f(i,j,k)=\max\{f(i,j,k),f(i+1,j-1,k)+2\}$
- $k>0$，修改$s_i$或$s_j$：$f(i,j,k)=\max\{f(i,j,k),f(i+1,j-1,k-1)+2\}$

## [Codeforces946G Almost Increasing Array](https://www.luogu.org/problem/CF946G)

被同一场的[F题](https://www.luogu.org/problem/CF946F)吓到了，然而$G$比$F$简单？

如果不带删除，其实是一个套路：

还是求最长上升子序列，考虑最朴素的方程$f(i)=\max\{f(j)\}+1(j<i,a_j<a_i)$

现在可以任意修改，要满足区间$[j,i]$能修改成上升的，所以还有个条件$i-j\le a_i-a_j$。

我们发现这三个大小关系可以合并成$j<i,a_i-i\ge a_j-j$，令$a_i$减去$i$，求一遍最长不下降子序列，答案就是$n-\max\{f(i)\}$。

考虑加上删除，让$f$再加一维$0/1$表示是否用过删除。

转移：

- $f(i,0)=\max\{f(j,0)\}+1(j<i,a_j\le a_i)$
- 在$j$之前用删除：$f(i,1)=\max\{f(j,1)\}+1(j<i,a_j\le a_i)$
- 在$[j+1,i-1]$之间用删除：$f(i,1)=\max\{f(j,0)\}+1(j<i-1,a_j\le a_i+1)$

由于在转移时没有考虑到被删的数不需要再修改了，所以答案为$\max\{n-\max\{f(i,0),f(i,1)\}+1,0\}$

离散化+树状数组就能优化到$O(n\log n)$。

## [Codeforces940E Cashback](https://www.luogu.org/problem/CF940E)

按分段长度$len$分别考虑：

- $len<c$，这时不会去掉任何数。无论$len$为多少代价都是这一段的和，把它看做$len$个长度为$1$的段处理。
- $len\ge c$。设它要去掉$k$个数。定义$kth(A,k)$为可重集$A$的前$k$小数的和。显然有$\sum\limits_{i=1}^kkth(A_i,1)\ge kth(\bigcup\limits_{i=1}^kA_i,k)$。所以当$len=c$时是最优的。

设$f(i)$为分完前$i$个数的最小代价。

根据上面两条，$f(i)=\min\{f(i-1)+a_i,f(i-k)+\min\{a_j\}\}(j\in[i-k+1,i])$。

要维护一个滑动窗口的$\min$，单调队列即可。

## [Codeforces505C Mr. Kitayuta, the Treasure Hunter](https://www.luogu.org/problem/CF505C)

大概是观摩一个沙雕网友过多了，从昨天下午起全机房都石乐志。我的智力水平再次刷新下限，已经切不动绿题了。{% emoji_coda  luotianyi/吃药 %}

很自然能想到设$f(i,j)$为跳到位置$i$，上一步跳了$j$步的最大宝藏数。

$O(30000\times 30000)$不大行。

我们发现真正有用的状态不多，可以队列优化$DP$，但是数组还是滚不起来。

于是我们得到了空间爆炸、时间无保证的~~优秀~~做法。

根据[逛公园](https://www.luogu.org/problem/P3953)的经验，把状态定义改为跳到位置$i$，上一步跳的步数与第一步的步数差值为$j$的最大宝藏数。

最差情况是第一步为$1$，之后依次递增，等差数列求和会发现$j$最大不超过$300$。

这就没了啊。

## [[TJOI2013]拯救小矮人](https://www.luogu.org/problem/P4823)

显然要让难出去的人先出去，按$a_i+b_i$排序。

设$f(i)$为跑了$i$个人剩下人的最大高度，搞个背包。

## [[ZJOI2006]超级麻将](https://www.luogu.org/problem/P2593)

设$f(i,j,k,0/1)$为考虑到第$i$张牌，第$i-1$张牌剩余$j$张，第$i-2$张牌剩余$k$张，是否打出过对子，前$i-3$张牌都已打完是否可行。

先在内部把打$2,3,4$张的情况考虑完：

$f(i,j,k,0/1)\rightarrow \begin{cases}f(i,j-3,k,0/1)\\f(i,j-4,k,0/1)\\f(i,j,k-3,0/1)\\f(i,j,k-4,0/1)\end{cases}$

$f(i,j,k,0)\rightarrow\begin{cases}f(i,j-2,k,1)\\f(i,j,k-2,1)\end{cases}$

最后考虑顺子，因为要保证前$i-3$张都打完，$k$是多少就要打多少个顺子：

$f(i,j,k,0/1)\rightarrow f(i+1,a_i-k,j-k,0/1)$

需要$DP$到$f(102,j,k,0/1)$，$f(103,0,0,1)$即为答案。

## [Codeforces846E Chemistry in Berland](https://www.luogu.org/problem/CF846E)

显然连上边是棵树。$DP$没啥难的，设$f(i)$为点$i$的需求量，$f(i)$为负表示有剩余。

贪心转移，如果儿子有剩余就全转过来，否则$i$需要给儿子转化。

$f(i)=a_i-b_i+\sum\limits_{j\in son(i)}f(j)\times [f(j)<0]+k_j\times f(j)\times[f(j)>0]$

判一下$f(1)$是否小于等于$0$就行。

然而这题很坑的地方是$f$的最大值$n\times a\times k$会爆$long\ long$。

可以高精，本来$DP$是$O(n)$的压压位没问题。

更巧妙的处理方法：观察方程，$f(i)$单次最多减少$b$，$n$个点最多减少$n\times b\le 10^{17}$。所以$f(i)$只要超过$10^{17}$，就不可能达到要求，直接输出"NO"即可。

## [Codeforces727F Polycarp's problems](https://www.luogu.org/problem/CF727F)

这都做不动是基本告别自行车了。{% emoji_coda xiaodianshi/fachou %}

最简单的是贪心。倒着考虑，这个问题本质上就是用前面的正数尽可能多的抵消后面的负数。剩下的交给$b_i$解决。

非常显然的普及贪心，一个正数要优先抵消较大的负数。维护一个堆倒着扫一遍。

最后把堆里的元素取出来做个前缀和，对每个询问二分出$b_i$最大能抵消到哪儿。为了照顾$DP$所以$n\le 750$，直接$O(nm)$枚举都行。

比贪心难想还不如贪心优的$DP$做法：

设$f(i,j)$为$i\sim n$删掉$j$道题最小需要的心情。

枚举$i$删或不删：$f(i,j)=\min\{f(i+1,j-1),\max\{f(i+1,j)-a_i,0\}\}$

最后在$f(1,i)$上二分或枚举。

