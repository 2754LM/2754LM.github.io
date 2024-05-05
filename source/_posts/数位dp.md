---
title: 数位dp
categories:
- 算法
- dp
tags:
- 算法
---



# 写在开头

本文dp数组基础讲的比较简略,看不懂请另查文章. 求解代码部分比较详细.

这篇写的累死我了(雾

# 题目

洛谷p2602数字计数https://www.luogu.com.cn/problem/P2602

给定两个正整数 a 和 b，求在 [a,b] 中的所有整数中，每个数码(digit)各出现了多少次。

# 思路

分别求[0,a]和[0,b]各个数码出现的次数,然后cnt[b] - cnt[a]就行了,这题a,b范围能到10^12^暴力显然超时,因为000-099和100-199和600-699这种后面两位中数码出现的次数是一样的,自然想到DP记录多少位对应多少次

# dp预处理

## 定义

dp[i]定义为i位数的每种数字有多少个(例如dp[2]代表00到99中某个数出现的次数,此处加前导0是为了使0和其他数一样)

一图流

![数位dp](数位dp.jpg)

<!-- more -->

## 公式

dp[i] = dp[i-1]*10 + 10^i-1^

递推公式是如何推导来的呢?

下面以数字2的出现举几个栗子(定义一下dp[0] = 0)

> 0到9,2只出现了一次dp[1] = dp[0]*10 + 10^0^
>
> 00到99呢, 我们分成两部分
>
> 一部分是小一位次数即dp[1]*10(因为0到9是1次, 10到19是一次,一共10次所以乘以10)
>
> 另一部分则是当前位次数即10^1^(因为20到29,一共有10^1^次)
>
> 所以我们得到了dp[2] = dp[1]*10 + 10^1^ 
>
> 最后看一下000到999
>
> 第一部分dp[2]*10(即000到099,100到199直到999一共十次)
>
> 第二部分10^2^(即200到299一共100次)
>
> ***强调一下22是2次,因为题目求的是2出现的次数,不是有2的数字出现的次数***

## 初始化

dp[0] = 0

## 递推顺序

正序

## 举例推导

公式部分已有列出(强烈建议看下文的一图流)



# 细节

## 数位限制

如23,

01,02,03...09中0出现十次其他0到9的数出现1次

11,12.....19中1出现十次其他0到9的数都1次

21...23

2为数位限制,需特判

## 前导0

01到99时,01,02这种前面的0是多余的,对0要特判

# 求解代码



## 递推

我们先设数字为ABCD

看A000，如果我们要求出它所有数位之和，我们会怎么求？

鉴于我们其实已经求出了0-9,0-99,0-999。。。上所有数字个数（f[i],且没有考虑前导0）我们何不把这个A000看成0000-1000-2000...A000对于不考虑首位每一个式子的数字的出现个数为 A*f[3]。加上首位出现也就是小于A每一个数都出现了10^3次，再加上，我们就把A000处理完了。

这样你以为就把第一位处理完了？不不不，首位A还出现了BCD+1次呢，也就是从A000~ABCD，这个A还出现了BCD+1次，所以再加上这些才行呢。那么你发现，我们成功把首位代表的所有数字个数求出来了，剩下的求解与A完全没有任何关系，只是BCD的求解，于是我们发现我们已经把一个大问题，化成了一个个小问题，也即是，对于一个这样n位的数，把他一位位的分离开来。

当然你还需要处理前导0你会发现前导0一定是0001,0002。。。0012，0013。。。0101,0102.。。0999这样的数，一共出现了10*（i-1)+10*(i-2)+...10 (i表示数字位数），让0的统计减去这个值，那么恭喜你这道题做完了。

一图流

![数位dp例](数位dp例.png)

首先dp预处理,然后我们分别求解a,b的数位出现次数,cnt[b[i]] - cnt[a[i]]就是a到b区间内数位i出现次数

初始化dp代码

```cpp
void intit(){
    ten[0] = 1; //10的i次方
    for(int i = 0; i <= n; i++){
        dp[i] = dp[i-1]*10 + ten[i-1];
        ten[i] = 10 * ten[i-1];
    }
}
```

求解[0,a]的数位出现次数代码(拆数)

```cpp
void solve(long long a, vector<long long>cnt){
    //数组按位存储a
    int num[n];//num[i]定义a的第i位数组
    int len = 0;
    while(a){
        num[++len] = a%10;
        a/=10;
    }
    
    //按位处理a(这里正序逆序都是可以的,只不过逆序和我们上面推导的思路一样,更好理解)
    for(int i = len; i >= 1; i--){
        //求(000-999)*A处理首位以外的数
        for(int j = 0; j <= 9; j++) cnt[j] += dp[i-1] * nums[i];
        //求0000-A000即处理首位比A小的数,即ten[3]次,(如2,在2000到2999的首位出现1000次)
        for(int j = 0; j < num[i]; j++) cnt[j] += ten[i-1];
        //求A000到ABCD即求首位A的数,答案即BCD+1次
		long long sum = 0;求BCD
		for(int j = i-1; j >= 1; j--){
			sum = sum*10 + num[j];
		} 
		cnt[num[i]] += sum+1;//A次数加BCD+1
        //特判前导0
        cnt[0] -= ten[i-1];
    }
}
```

## 记忆化搜索(dfs)

每次查找完某一个数出现的次数

### 定义

dp[pos] [sum] [lead] [limit]表示限制条件中某数码出现的次数

pos表示最低要求的数前有几位

sum表示要求数开始有几位

>  如要求2,dp[2] [1] 就是200-299, dp[2] [2] 表示2200-2299

lead表示有无前导0

> 有为true,否位false

limit代表数位限制

> 如果能取到0-9取false,不能则取true

一图流(以处理324的2次数为例)

![数位dp记忆化](数位dp记忆化.png)



看不懂没关系,跟着下面的代码一步步分析(求0-a的数码次数)

```cpp
long long dp[n][n][2][2];
int num[n];//num[i]定义为a的第i位
int cur;//当前统计的数字

long long dfs(int pos, int sum, bool lead, bool limit){
    long long ans = 0;
    if(pos == 0) return sum; //因为我们现在求的是cur,sum表示前面有几个cur,比如220求2,sum是2,那我们求221,223,224,225都可以直接用这个dp[pos][sum]...和上面的递推写法区别就在,这里我们返回的是某一个具体数中的数码出现次数,后面可以重复利用这个结论.而递推写法在一开始就推导出来20-29中2的次数,220到229可以推导出来
    if(dp[pos][sum][lead][limit] != -1){
        return dp[pos][sum][lead][limit];
    }
    
    int up; //最高位
    if(limit) up = num[pos];//如果有数位限制,那就等于当前数,如324,当前首位位百位的话,就为3
    else up = 9;//如果无就为9,如324,当前首位为十位的话,没有限制就是9
    
    //假如我们在求0-324,cur为2,此时up为3
    for(int i = 0; i <= up; i++){
        if(i == 0 && lead) ans += dfs(pos-1, sum, true, limit && (i==up));
             //求000-099(有前导0的),进下一层dfs是求000-009,010-019,020-029,......090-099(始终有前导0的)
        
        else if(i == cur) ans += dfs(pos-1, sum + 1, false, limit && (i==up));
             //200-299(首位为要统计的数时),进一层(sum变成了1,代表首位固定是2了),下一层此处求的是220-229

        else if(i != cur) ans += dfs(pos-1, sum, false, limit && (i==up));
    	//求100-199(首位不为要统计的数且无前导0时),进下一层是求110-119,130-139这样的(首位不为0且不为统计数的)
       //还有求300-324,因为此时i==up==3所以下一层还是有限制的,下一层求320-324
    }
    //单独解释一下limit && (i==up)这个条件,是专门处理300-324,20-24这种限制位的
    dp[pos][sum][lead][limit] = ans;//记忆化
    return ans;
}
//按位存a
long long solve() {
    int len = 0;
    while(a){
        num[++len] = a%10;
        a/=10;
    }
    memset(dp, -1, sizeof(dp));//初始化
    return dfs(len,0,true,true);
}

int main(){
    long long a;
    cin>>a;
    for(int i = 0; i <= 9; i++) {
        cur = i;
        cout<<solve(a);//依次输出0到9在[0,a]中出现的次数
    }    
    return 0;
}
```



# 相关题目

洛谷P4999 烦人的数学作业

难度: 普及+/提高

https://www.luogu.com.cn/problem/P4999

洛谷P2657windy 数

难度: 提高+/省选-

https://www.luogu.com.cn/problem/P2657

p2657的实现(用的记忆化模板)

```cpp
#include<bits/stdc++.h>
using namespace std; 
const int n = 15;
long long dp[n][n][2][2];
int num[n];
//last表示上一位
long long dfs(int pos, int last, bool lead, bool limit){
	long long ans= 0;
	if(pos == 0) return 1;
	if(dp[pos][last][lead][limit] != -1) return dp[pos][last][lead][limit];
	int up = 9;
	if(limit) up = num[pos];
	for(int i = 0; i <= up; i++){
		if(abs(i-last) < 2) continue;
		if(i == 0 && lead) ans += dfs(pos-1,-2,true,limit&&i==up);//即都是前导0,后一位随便选..比如0012,0是前导0,当前也是0,1开始才有意义,1的位置随便选 
		else ans += dfs(pos-1,i,false,limit&&i==up); //其他情况,哪怕有前导0这里也要记为没有,因为0123的1还是会限制2的选择
	}
	dp[pos][last][lead][limit] = ans;
	return ans;
}

long long solve(int a) {
    int len = 0;
    while(a){
        num[++len] = a%10;
        a/=10;
    }
    memset(dp, -1, sizeof(dp));//初始化
    return dfs(len,-2,true,true);//-2代表随便选 ,首位肯定随便选 
}

int main(){
    long long a,b;
    cin>>a>>b;
    cout<<solve(b) - solve(a-1)<< " ";
    return 0;
}
```

洛谷P4124 手机号码

难度:提高+/省选-

https://www.luogu.com.cn/problem/P4124

洛谷p4798 卡尔文球锦标赛

难度:省选/NOI-

https://www.luogu.com.cn/problem/P4798

洛谷P3281 数数

难度:省选/NOI-

https://www.luogu.com.cn/problem/P3281

洛谷p2518 计数

难度:省选/NOI-

https://www.luogu.com.cn/problem/P2518

洛谷p3286 方伯伯的商场之旅

难度:省选/NOI-

https://www.luogu.com.cn/problem/P3286