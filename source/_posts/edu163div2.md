---
title: edu163div2D
date: 2024-03-16 22:05:34
tags:
- 比赛
categories:
- 比赛

---

https://mirror.codeforces.com/contest/1948/problem/D

> 题目大意:求最长串联重复子串
>
> 串联重复(长度偶数)前后相等,如abab(ab = ab)
>
> 子串,连续的(不连续的叫子序列)

这场的D有点意思,一开始只写出了n3复杂度的,理所当然的被hack了 、、

> n <= 5000

记一下n2的想法

3次做法如下,最容易想的暴力,枚举起点终点,判断这个范围行不行

```cpp
#include<bits/stdc++.h>
#define endl '\n'	
using namespace std;
typedef long long ll;

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	int T;
	cin>>T;
	while(T--){
		string s;
		cin>>s;
		int n = s.size();
		int ans = 0;
		for(int i = 0; i < n; i++){ //枚举起点
			if(ans > n-i) break; 
			for(int j = i + 1; j < n; j++){  //枚举终点
				if(j - i > n - j) break;
				int l = i, r = j;
				int sum = 0;
				bool flag = 0;
				while(l < j && r < n){  //判断这个范围
					if(s[l] == s[r] || s[l] == '?' || s[r] == '?'){
						 sum+=2;
						 l++;
						 r++;
					}
					else{
						flag = 1;
						break;
					}
				}
				if(!flag) ans = max(ans, sum);
			}
		} 
		cout<<ans<<endl;
	} 
	return 0;
}
```

平方做法如下,主要利用了子串的连续性进行优化
>其实就是直接跳到失匹位置

如acabazab,len为4时

**a**cab **a**zab相等cnt+1

a**c**ab a**z**ab不相等cnt = 0

类推,最长是2

```cpp
for(int len = n/2; len; len--){//逆序枚举长度
	cnt = 0;
	for(int i = 0; i + len < n; i++){//枚举起点
		if(s[i] == s[len+i] || s[i] == '?' || s[len+i] == '?') cnt++;
		else cnt = 0;
		if(cnt == len) return len * 2;
	}
} 
```