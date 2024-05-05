---
layout: blog
title: 概率dp
date: 2024-04-20 22:47:57
tags: 算法
cate: 
- 算法
- 杂谈

---

今天打abc遇到的记录一下

[E - Toward 0 (atcoder.jp)](https://atcoder.jp/contests/abc350/tasks/abc350_e)

题目大意：给你一个整数x，你有两种操作

操作1：花费x元，把x变成x/a（a是给定的数字）

操作2：花费y元，把x变成x/b（b是1到6中随机一个数字，概率相等）

问，最少多少米，能让x变0

记dp[i]是当前整数是i时，花费了的钱数

```cpp
//公式如下，两种情况
//操作1: dp[i] = dp[i/a] + x;
/*操作2：dp[i] = (dp[i] + dp[i/2] + dp[i/3] + dp[i/4] + dp[i/5] + dp[i/6]) / 6 + y;
化简把dp[i]转到一侧（防止死循环）
dp[i]*5/6 = (dp[i] + dp[i/2] + dp[i/3] + dp[i/4] + dp[i/5]) / 6 + y;
dp[i] = y * 6/5 + (dp[i] + dp[i/2] + dp[i/3] + dp[i/4] + dp[i/5])/5;
*/
```



```cpp
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
map<ll, double>mp;
ll n,a,x,y;
double dfs(ll t){
    if(t == 0) return 0;
    if(mp[t]) return mp[t];
    double ans1 = dfs(t/a) + x;
    double ans2 = y*6/5.0; //投到1的话，花费
    for(int i = 2; i <= 6; i++){
        ans2 += dfs(t/i) / 5.0;
    }
    mp[t] = min(ans1, ans2);
    return mp[t];
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
    cin>>n>>a>>x>>y;
    cout<<fixed<<setprecision(8)<<dfs(n);
	return 0;
}

```
