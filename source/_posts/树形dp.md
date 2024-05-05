---
title: 树形dp
categories:
- 算法
- dp
tags:
- 算法
---

# 常见定义

dp[i]第一维即根节点编号

一般自底向上类似dfs,即先遍历到底

# 题目

## 洛谷p1352上司的舞会(经典)

https://www.luogu.com.cn/problem/P1352

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N = 6005;
vector<int> G[N];
int dp[N][2],val[N],father[N];

//dp定义,dp[i][j]选或不选i节点的值(j为1选,0不选) 
void dfs(int u){
	dp[u][0] = 0;
	dp[u][1] = val[u];
	for(int i : G[u]){
		dfs(i);//遍历子节点
		dp[u][1] += dp[i][0];//父选子不选 
		dp[u][0] += max(dp[i][0], dp[i][1]);//父不选,子选或不选 
	}	
} 

int main(){
	int n;
	cin>>n;
	for(int i = 1; i <= n; i++){
		cin>>val[i];
	}
	for(int i = 1; i < n; i++){
		int to,from;
		cin>>to>>from;
		G[from].push_back(to);
		father[to] = from;
	}
	int t = 1;
	while(father[t]) t = father[t];//找根节点
	dfs(t);
	cout<<max(dp[t][0], dp[t][1]);
	return 0; 
} 
```

<!-- more -->

## 洛谷P2015 二叉苹果树(树形背包)

https://www.luogu.com.cn/problem/P2015#submit

树形背包,过段时间再写下

```cpp
#include <bits/stdc++.h>
using namespace std;
vector<int> v[109];
int dp[109][109];
int cost[109][109];
int n,need;
int use[109];

void dfs(int cur){
	use[cur] = 1;
	for(int nx : v[cur]){
		if(use[nx] == 1) continue;
		use[nx] = 1;
		dfs(nx);
		for(int j = need; j >= 1; j--){
			for(int k = j-1; k >= 0; k--){
				dp[cur][j] = max(dp[cur][j], dp[nx][k] + dp[cur][j-k-1] + cost[cur][nx]);
			}
		}
	}
} 

int main() {
	cin>>n>>need;
	for(int i = 1; i < n; i++){
		int u,to,w;
		cin>>u>>to>>w;
		v[u].push_back(to);
		v[to].push_back(u);
		cost[u][to] = w;
		cost[to][u] = w;
	} 
	//dp[i][j]定义i节点,留j个树枝时的最大苹果数
	//方程dp[i][j] = 子节点1[k]+子节点2[j-k]
	//初始化,遇到根结点就返回苹果数
	//方向,树根到叶子
	dfs(1);
	cout<<dp[1][need];
	return 0;
}
```

## 洛谷 P1273 有线电视网(树形背包)

https://www.luogu.com.cn/problem/P1273

写完这题我只想和树形背包爆了,去***的树形dp

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 3005;
struct node{
	int v,w;
	node(int v = 0, int w = 0) : v(v),w(w){}
};
vector<node> edge[N];
vector<vector<int>>dp(N,vector<int>(N,INT_MIN/2)); 
int val[N];
int n,m;

int dfs(int u){
	if(u>n-m)
	{
		dp[u][1]=val[u];
		return 1;
	} 
	int sum = 0;
	for(int i = 0; i < edge[u].size(); i++){
		int v = edge[u][i].v, w = edge[u][i].w;
		sum += dfs(v);
		for(int j = sum; j >= 0; j--){
			for(int k = 0; k <= j; k++){
				dp[u][j] = max(dp[u][j], dp[u][j-k] + dp[v][k] - w);
			}
		}
	}
	return sum;
}

int main() {
	//构图 
	cin>>n>>m;
	int u = 0; 
	for(int i = 1; i <= n-m; i++){
		int k;
		u++;
		cin>>k;
		while(k--){
			int v, w;
			cin>>v>>w;
			edge[u].push_back(node(v,w));
		}
	}
	for(int i = n-m+1; i <= n; i++){
		cin>>val[i];
	}
	//初始化 
	for(int i = i; i <= n; i++) dp[i][0] = 0;
	dfs(1);
	for(int i = m; i >= 0; i--){
		if(dp[1][i] >= 0) {
			cout<<i;
			break;
		}
	}
	return 0;
}
```

