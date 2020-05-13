---
title: 递归求逆元效率の胡乱分析
date: 2019-11-03 09:59:22
categories: 颓
tags:
	- 数论
	- 逆元
keywords: 逆元
description: 其实一直想做这个测试了，但是没时间（懒）。
photos: https://cdn.jsdelivr.net/gh/ctz45562/cdn@1.7.9/dgqnyxldhlfx.jpg
mathjax: true
---

其实一直想做这个测试了，但是没时间~~（懒）~~。

某个下午没干劲做题，不想荒废于是搞了出来。

<!--more-->

# 引子

这种求逆元的方法其实是半年前指挥安利给我的。

众所周知，逆元可以$O(n)$递推：

``` cpp
const int mod = 998244353;
int inv[maxn],n;
int main(){
	for(register int i=1;i<=n;++i)inv[i]=1ll*(mod-mod/i)*inv[mod%i]%mod;
}
```

这只适用于模数较小或用的逆元个数有限的情况。

但我们可以把递推改成递归：

``` cpp
const int mod = 998244353;
int inv(int x){return x==1?1:1ll*(mod-mod/x)*inv(mod%x)%mod;}
```

这样可以求出任意数的逆元。如果会出现$0$要特判。

这种方法好像也没有什么专业的名称，我姑且称其为「递归式逆元」

优势非常明显，特别短，只有一行。

但它依然不适用于模数非质数的情况，而且复杂度未知。

接下来就是关于其复杂度的胡搞。

# 尝试证明

这个复杂度不好证明。

每次$x$会变为$mod\%x$，这并不能保证$x$会缩小为原来的多少。（$exgcd$每次$a$至少缩小一半所以保证其复杂度是$\log$的）

在洛谷上得到了一篇知乎的证明：[戳这儿](https://www.zhihu.com/question/59033693)。

大概意思是上界大约为$O(n^{\frac{1}{3}})$，下界为$O(\dfrac{\ln n}{\ln\ln n})$，期望为$O(\log mod)$。

这个复杂度听起来很爆炸。

好证明不如烂测试。直接挑选几个模数测试$1\sim mod-1$里最大递归次数：

``` cpp
const int mod = 998244353;
int cnt[maxn];
int inv(int x){if(x<maxn)return cnt[x];return inv(mod%x)+1;}
int main(){
    int ans=0;
    for(register int i=2;i<maxn;++i)ans=max(ans,cnt[i]=cnt[mod%i]+1);
    for(register int i=maxn;i<mod;++i)ans=max(ans,inv(i));
    printf("%d\n",ans);
}
```

结果：

|     模数      | 最大递归次数 |
| :-----------: | :----------: |
|  $998244353$  |     $44$     |
|   $10^9+7$    |     $48$     |
|   $10^9+9$    |     $46$     |
|   $10^6+7$    |     $25$     |
|  $104857601$  |     $38$     |
| 某$8$位大质数 |     $36$     |

这至少说明了在一些常用模数下，复杂度还是可以接受的。

可以看出最大递归次数与模数是近似的正相关，但是也有$10^9+7$和$10^9+9$的反例。

# 比较

其他求逆元的方法无非就是基于费马小定理的快速幂和$exgcd$了。

论适用性的话还是$exgcd$，能处理模数非质数的情况。

以下只比较效率。

由于取模和除法效率远低于其他运算，只考虑这两种。同时还考虑了递归开栈和传入变量。

## 快速幂

``` cpp
inline int quickpow(int x,int y){
	int ans=1;
    while(y){
		if(y&1)ans=1ll*ans*x%mod;
        x=1ll*x*x%mod,y>>=1;
    }
}
```

运行$\log mod$次，一般跑不满。每次涉及$2$次取模，没有递归。

## exgcd

``` cpp
void exgcd(int a,int b,int &x,int &y){
    if(!b)x=1,y=0;
    else exgcd(b,a%b,y,x),y-=x*(a/b);
}
inline int inv(int a){
 	int x,y;
 	exgcd(a, mod, x, y);
 	return (x+mod)%mod;
}
```

运行$\log mod$次，一般跑不满。每次涉及$1$次取模、$1$次除法。

需要递归实现，每层递归传入$2$个$int$和$2$个$int\&$。

## 递归式

``` cpp
int inv(int x){return x==1?1:1ll*(mod-mod/x)*inv(mod%x)%mod;}
```

运行$?$次（参考上面数据）。每次涉及$2$次取模、$1$次除法。

需要递归实现，每层传入$1$个$int$。

## 小结

理论上快速幂的性能是最高的，$exgcd$常数不小，递归式复杂度和常数都很大。

但是前两种递归式都不好优化，递归式有一个非常棒的优化：预处理。

``` cpp
int inv[maxn]={0,1};
int INV(int x){
    if(x<maxn)return inv[x];
    return 1ll*(mod-mod/x)*INV(mod%x)%mod;
}
int main(){
    for(register int i=2;i<maxn;++i)inv[i]=1ll*(mod-mod/i)*inv[mod%i]%mod;
}
```

也可以记忆化，但估计是负优化而且难写。

然而以上都是纸上谈兵，只是理论~~瞎~~分析了一波常数。实测才是硬道理。

# 实测

以下测试都只用最常见的$998244353$和$10^9+7$，因为`clock()`是厌氧生物所以都没开$O2$。

当初指挥给我安利递归式的时候，进行了随机数据下的测试，结果是递归式跑的飞起。

那么把$1\sim mod-1$全跑一遍呢？

对快速幂、$exgcd$、递归式、预处理递归式（预处理至$10^7$，占用空间$38MB$，测试时算上预处理时间）进行测试。

测试代码：

```cpp
#include <iostream>
#include <cstdio>
#include <algorithm>
#include <cmath>
#include <cstring>
#include <time.h>

#define maxn 10000005

const int mod = 998244353;

using namespace std;

namespace inv1{
	inline int quickpow(int x,int y){
		int ans=1;
		while(y){
			if(y&1)ans=1ll*ans*x%mod;
			x=1ll*x*x%mod,y>>=1;
		}
		return ans;
	}
}
namespace inv2{
	void exgcd(int a,int b,int &x,int &y){
    	if(!b)x=1,y=0;
    	else exgcd(b,a%b,y,x),y-=x*(a/b);
	}
	inline int inv(int a){
 	   int x,y;
 	   exgcd(a,mod,x,y);
 	   return (x+mod)%mod;
	}
}
namespace inv3{
	int inv(int x){return x==1?1:1ll*(mod-mod/x)*inv(mod%x)%mod;}
}
namespace inv4{
	int inv[maxn];
	void sieve(){
		inv[1]=1;
		for(register int i=2;i<maxn;++i)inv[i]=1ll*(mod-mod/i)*inv[mod%i]%mod;
	}
	int INV(int x){
		if(x<maxn)return inv[x];
		return x==1?1:1ll*(mod-mod/x)*INV(mod%x)%mod;
	}
}
int main(){
	printf("Speed test under mod %d.\n",mod);
	int s=clock();
	for(register int i=1;i<mod;++i)inv1::quickpow(i,mod-2);
	s=clock()-s;
	printf("quickpow : %d ms\n",s);
	s=clock();
	for(register int i=1;i<mod;++i)inv2::inv(i);
	s=clock()-s;
	printf("exgcd : %d ms\n",s);
	s=clock();
	for(register int i=1;i<mod;++i)inv3::inv(i);
	s=clock()-s;
	printf("recursive : %d ms\n",s);
	s=clock();
	for(register int i=1;i<mod;++i)inv4::INV(i);
	s=clock()-s;
	printf("recursive with preprocess: %d ms\n",s);
}
```

~~不要在意我胡写的英语~~

结果：

$998244353$：

|     方法     | 时间（ms） |
| :----------: | :--------: |
|    快速幂    |   162268   |
|   $exgcd$    |   260005   |
|    递归式    |   270803   |
| 预处理递归式 |   99148    |

$10^9+7$也没啥区别：

|     方法     | 时间（ms） |
| :----------: | :--------: |
|    快速幂    |   175463   |
|   $exgcd$    |   270038   |
|    递归式    |   328440   |
| 预处理递归式 |   103875   |

预处理竟恐怖如斯{% emoji_coda quyin/amazing %}，甚至一度突破$100s$。

缺点在于占用空间。可以少预处理一些以时间换空间。

再一个预处理递归式适用范围可能不是很广，并没有什么题会让你把$1\sim mod-1$的逆元全求一遍。拿来卡常的话预处理自身已经需要$200+ms$了。

其他方法里快速幂是最优秀的了，而$exgcd$和递归式常数确实大。

# 总结

逆元用什么方法还是看习惯吧。

通过这些测试，我收获了：

- 一篇水文
- 颓废的快乐
- 少量的干劲
- “没有荒废”的自我安慰

以上。