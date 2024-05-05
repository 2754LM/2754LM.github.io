---
layout: blog
title: lca
date: 2024-03-07 10:35:34
tags: 算法
categories: 
- 算法
- 图论
---

# 倍增法

```c++
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
const int N = 500005;
vector<int> e[N*2];
int fa[N][30]; //注意一下fa溢出,int干到31位就差不多了
int deep[N];

void dfs(int u, int pre){
	deep[u] = deep[pre] + 1;
	fa[u][0] = pre;
	for(int i = 1; (1 << i) <= deep[u]; i++){
		fa[u][i] = fa[fa[u][i-1]][i-1];
	}
	for(int v : e[u]){
		if(v == pre) continue;
		dfs(v, u);
	}
}
int lca(int x, int y){
	if(deep[x] < deep[y]) swap(x, y);
	for(int i = 25; i >= 0; i--){
		if(deep[x] >= deep[y] + (1<<i)){
			x = fa[x][i];
		}
	}
	if(x == y) return x;
	for(int i = 25; i >= 0; i--){
		if(fa[x][i] != fa[y][i]){
			x = fa[x][i];
			y = fa[y][i];
		}
	}
	return fa[x][0];
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	int n,q,root;
	cin>>n>>q>>root;
	for(int i = 1; i < n; i++){
		int x, y;
		cin>>x>>y;
		e[x].push_back(y);
		e[y].push_back(x);
	}
	deep[0] = 0;
	dfs(root, 0);
	for(int i = 0; i < q; i++){
		int x,y;
		cin>>x>>y;
		cout<<lca(x, y)<<endl;
	}
	return 0;
}
```
