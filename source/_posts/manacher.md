---
title: manacher
date: 2023-12-28 22:14:44
tags:
- 算法
categories:
- 算法
- 字符串
---

# 定义

马拉车,是一种动态规划算法,用来求解字符串S中最长回文子串的长度,时间复杂度O(n)

# 思路

回文数有两种,奇数和偶数,如aba和abba,奇数好求(中心扩展法)

假设S为abba

我们在相隔字符间加入#,最终为

#a#b#b#a#长度必为奇数

为了编程方便,首位加入哨兵?#a#b#b#a#@

定义数组p[],p[i]代表以s[i]为中心字符的最长回文串半径(自身为1)

如

?#a#b#b#a#@

11212521211

最大的p[i] - 1就是答案,此处为p[5]-1 = 5- 1 = 4

马拉车主要解决求p的过程,核心思想是**回文的镜像也是回文**

懒得写了,直接看代码吧

<!-- more -->

# 实现

洛谷3805https://www.luogu.com.cn/problem/P3805

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 1.1*1e7+5;
char s[2*N];
int p[2*N];
int n;

void manacher(){
	int R = 0, C;
	for(int i = 1; i < n; i++){
		if(i < R){ //可以镜像 
			//直接翻转,(i+j)/2 = C,推导 j = 2*c - i
			//越界情况,最长C + p[C] - i,然后再暴力拓展 
			p[i] = min(p[C*2 - i], C + p[C] - i);
		}else{//反之先赋1 
			p[i] = 1;
		}
		while(s[i + p[i]] == s[i - p[i]]){//暴力扩展 
			p[i]++;
		}
		if(i + p[i] > R){
			R = i + p[i];
			C = i;
		}
	}
}

int main()
{
	string tmp;
	cin>>tmp;
	n = tmp.size();
	int k = 0;
	s[k++] = '?';
	s[k++] = '#';
	for(int i = 0; i < n; i++) {
		s[k++] = tmp[i];
		s[k++] = '#';
	}
	s[k++] = '$'; //首尾不同防止越界 
	n = k;
	manacher();
	int ans = 0;
	for(int i : p) ans = max(ans, i);
	cout<<ans-1; 
}
```

