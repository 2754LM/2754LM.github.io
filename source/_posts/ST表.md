---
title: ST表
date: 2023-12-28 22:14:44
tags:
- 数据结构
categories:
- 数据结构
- ST表
---

# 定义

利用倍增思想,求解静态RMQ(区间最值查询)的算法

利用O(nlog2n)复杂度预处理,进行O(1)的查询

> 倍增思想: 2的i次增长

# 模板

先谈一下为什么开log2啊? 如果不开,你长度都是2,4,6这样访问,不浪费空间吗?N*N稍微大点直接爆内存了

而且你初始化个数组查某个数的最大2的次数, 这样和log2比不方便,何况懒得写log2还有自带的函数.

```cpp
#include<bits/stdc++.h>
using namespace std;
const int N = 1e5+5;
int st[N][21]; //st[i][j],i记录的是现在遍历的下标,j记录的是log2(长度)
int lg[N]; //因为二进制就是2的i次,所以用来记录很方便,位移就可以了.
int n,m;

void init(){
	lg[0] = -1;
	for(int i = 1; i <= n; i++){
		lg[i] = lg[i>>1] + 1;
	}
	for(int i = 1; i <= n; i++){
		cin>>st[i][0];
	} 
	for(int j = 1; j <= lg[n]; j++){
		for(int i = 1; i <= n - (1<<(j-1)) +1; i++){
			st[i][j] = min(st[i][j-1], st[i + (1<<(j-1))][j-1]); //j-1就是上一个log2(上一层长度),我们<<位移就为了展开来定下i的位置
		}
	}
} 

int query(int l, int r){
	int k = lg[r-l+1];
	return min(st[l][k], st[r-(1<<k)+1][k]); //和初始化是同理的,log2上一层就是k
}

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	cin>>n>>m;
	init();
	for(int i = 0; i< m; i++){
		int l,r;
		cin>>l>>r;
		cout<<query(l,r)<<" ";
	}
	return 0;
}
```

