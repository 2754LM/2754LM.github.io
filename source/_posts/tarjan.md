---
title: tarjan
date: 2024-03-05 14:58:37
tags:
- 算法
categories:
- 算法
- 图论
---

tarjan算法的应用很多,简单介绍一下割点(边), 点(边)双连通,缩点. tarjan还可以解决离线的lca问题

# 前置知识

dfs优先生成树

# 割点(边)

核心思想:dfn数组(时间戳),low数组(最早子节点)

dfn记录递归第一次进入各个节点的时间

low数组记录接下来可以回到的最早祖先(单向走)

## 割点

https://www.luogu.com.cn/problem/P3388

下文有一个v != fa,如果用vis记录,else if(vis[v]){这样,还是会访问一次父节点,但这个题目里,所有节点整体都访问父节点,整体都减小1,就没什么影响.

1到2,2到1这样的双向点,如果你用fa记录,肯定是不能重复的,用vis就可以,所以下面缩点的题目必须用vis而不是fa,因为这两个点也是强连通分量

有向图有横叉边,所以用vis判断,无向图没有横叉边,就用不太着

```cpp
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
const int N = 1e5;
int low[N], dfn[N];
bool ans[N];
vector<int> e[N];
int n,m;
int r;
int cnt = 0;
int num;

void dfs(int u, int fa){
	low[u] = dfn[u] = ++cnt;
	int son = 0;
	for(int v : e[u]){  
		if(!dfn[v]){ //没访问过 
			son++;
			dfs(v, u);
			low[u] = min(low[u], low[v]);
			if(u != r && low[v] >= dfn[u]){
				num += !ans[u];
				ans[u] = 1;
			}
		}else if(v != fa){ //访问过,但v不等于fa 
			low[u] = min(low[u], dfn[v]);
		}
	}
	if(u == r){
		if(son > 1){
			num += !ans[u];
			ans[r] = 1;
		}
	}
}

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	cin>>n>>m;
	for(int i = 0; i < m; i++){
		int x,y;
		cin>>x>>y;
		e[x].push_back(y);
		e[y].push_back(x);
	}
	for(int i = 1;  i <= n; i++){
		if(!dfn[i]){
			r = i;
			dfs(i, 0);
		}
	} 
	cout<<num<<endl;
	for(int i = 1; i <= n; i++){
		if(ans[i]) cout<<i<<" ";
	}
	return 0;
}
```

<!-- more -->

# 点(边)双连通

## 点双连通

https://www.luogu.com.cn/problem/P8435

用stack记录访问,遇到割点的时候,stack弹出到根节点(此刻形成一个最大连通分量),再由性质:割点至少连接两个双连通分量,根节点(割点)留一回合

还有单个节点也算是连通分量

```cpp
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
const int N = 500005;
vector<int> e[N];
int low[N], num[N];
vector<int> ans[N];
int n,m;
int cnt;
stack<int>st;
int bcc = 0;

void dfs(int u, int fa, int root){
	int son = 0;
	low[u] = num[u] = ++cnt;
	st.push(u);
	for(int v : e[u]){
		if(!num[v]){
			son++;
			dfs(v, u, root);
			low[u] = min(low[u], low[v]);
			if(low[v] >= num[u]){
				bcc++; 
				int p = 0;
				while(p != v){ //把u也去了,放ans里 
					p = st.top();
					ans[bcc].push_back(st.top());
					st.pop();
				}
				ans[bcc].push_back(u); //把u补回去(割点至少在两个双连通分量里) 
			}
		}else if(v != fa){
			low[u] = min(low[u], num[v]);
		}
	}
	if(!son && u == root){ //独立情况 
		ans[++bcc].push_back(u);
	}
}

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	cin>>n>>m;
	for(int i = 0; i < m; i++){
		int u,v;
		cin>>u>>v;
		e[u].push_back(v);
		e[v].push_back(u);
	} 
	for(int i = 1; i <= n; i++){
		if(low[i]) continue;
		dfs(i, 0, i);
	}
	cout<<bcc<<endl; 
	for(int i = 1; i <= bcc; i++){
		cout<<ans[i].size()<<" ";
		for(int j : ans[i]) cout<<j<<" ";
		cout<<endl;
	}
	return 0;
}
```

## 边双连通

https://www.luogu.com.cn/problem/P8436#submit

如何快速存储查找反向边?

我们可以通过异或运算来解决,假如现在有双向边,存两次,第一个为2,第二个为3, 3^1 = 2.即二进制最低位上有两种情况,非常适合成对的边,我们直接^就可以了

另外要注意,如果数组下标从1开始,异或前要先左移一位,或者异或0,如果从0开始就不用移动.为了方便,可以全部写成左移一位的形式,有效避免了判断

如果是链式前向星,cnt本来就是用来记录边数的,直接cnt ^ 1就可以了

如果是vector,需要在添加元素时,多加一个参数,即cnt

### 法一

tarjan求出所有桥,dfs每个不经过桥的分块.

用常见的海岛题来说,桥就是海,其他区域就是岛,求岛总数

注意存桥的方法,在此节开头已经说明

````cpp
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
const int N = 5e5 + 10;
const int M = 4e6+10; 
vector<pair<int,int>> e[M];
int low[N], dfn[N];
bool bridge[M];
int cnt = 1;
void tarjan(int u, int fa){
	low[u] = dfn[u] = cnt++;
	for(auto i : e[u]){
		int v = i.first;
		if(!dfn[v]){
			tarjan(v, u);
			low[u] = min(low[u], low[v]);
			if(low[v] > dfn[u]){
				bridge[i.second] = bridge[i.second^1] = 1;
			}
		}else if(v != fa){  //可能会漏了重边,可以把fa改成记录父边的出现时间i.second != fa^1
			low[u] = min(low[u], dfn[v]);
		}
	}
}
vector<vector<int>>ans;
bool vis[N];
int sum = 0;
void dfs(int u){
	vis[u] = 1;
	ans[sum].push_back(u);
	for(auto i : e[u]){
		int v = i.first;
		if(bridge[i.second] || vis[v]) continue;
		dfs(v);
	}
}

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	int n,m;
	cin>>n>>m;
	for(int i = 1; i <= m; i++){
		int x,y;
		cin>>x>>y;
		if(x == y) continue;
		e[x].emplace_back(y, i << 1);  //记录边序号,方便存方向边
		e[y].emplace_back(x, i << 1 | 1);
	}
	for(int i = 1; i <= n; i++){
		if(!dfn[i]) tarjan(i, 0);
	}
	for(int i = 1; i <= n; i++){
		if(!vis[i]){
			ans.push_back(vector<int>());
			dfs(i);
			sum++;
		}
	}
	cout<<sum<<endl;
	for(auto i : ans){
		cout<<i.size()<<" ";
		for(int j : i) cout<<j<<" ";
		cout<<endl;
	}
	return 0;
}
````



### 法二

类似求强连通分量和点双的写法

# 缩点

https://www.luogu.com.cn/problem/P3387

首先找到强连通分量,把它们缩成一个点

```cpp
#include<bits/stdc++.h>
#define endl '\n'
using namespace std;
typedef long long ll;
const int N = 1e5;
vector<int> e[N];
int val[N];
//tarjan
int dfn[N], low[N];
int cnt = 1;  //时间 
stack<int>st;  
int num;  //记录强连通分量数量 
int bel[N];  //所属强连 
int sum[N];  //强连数值 
bool vis[N]; //有没有找到强连 
void tarjan(int u, int fa){
	st.push(u);
	dfn[u] = low[u] = cnt;
	cnt++;
	for(int v : e[u]){
		if(!dfn[v]){
			tarjan(v, u);
			low[u] = min(low[u], low[v]);
		}else if(!vis[v]){  //如果没找到v所在的强连通分量 
			low[u] = min(low[u], dfn[v]);
		}
	}
	if(low[u] == dfn[u]){
		int p;
		num++;
		do{
			p = st.top();
			bel[p] = num;
			vis[p] = 1;  //找到了p所在的强连通分量 
			sum[num] += val[p];
			st.pop();
		}while(p != u);
	}
}
vector<int> edge[N]; //新图 
vector<int> edger[N]; //新图的反图 
int in[N], h[N]; //拓扑
int ans[N]; //拓扑顺序 

void topo(){
	int tmp = 1;
	queue<int>q;
	for(int i = 1; i<= num; i++){
		if(in[i] == 0) q.push(i);
	} 
	while(!q.empty()){
		int u = q.front();
		q.pop();
		ans[tmp++] = u;
		for(int v : edge[u]){
			in[v]--;
			if(in[v] == 0) q.push(v);
		}
	}
} 

int main(){
	ios::sync_with_stdio(0);
	cin.tie(0), cout.tie(0);
	int n,m;
	cin>>n>>m;
	for(int i = 1; i <= n; i++){
		cin>>val[i];
	}
	for(int i = 0; i < m; i++){
		int u,v;
		cin>>u>>v;
		e[u].push_back(v);
	}
	for(int i = 1; i <= n; i++){
		if(!dfn[i]) tarjan(i, 0);
	}
	for(int i = 1; i <= n; i++){
		for(int j : e[i]){
			if(bel[i] != bel[j]){
				edge[bel[i]].push_back(bel[j]);
				edger[bel[j]].push_back(bel[i]);
				in[bel[j]]++;
			}
		}
	}
	topo();  //新图找拓扑,反图找父节点 
	int f[N];
	for(int i = 1; i <= num; i++){
		int cur = ans[i];
		f[cur] = sum[cur]; 
		for(int j : edger[cur]){ //遍历新图的反图 
			f[cur] = max(f[cur], sum[cur] + f[j]);
		}
	}
	int a = 0;
	for(int i = 1; i <= num; i++){
		a = max(a, f[i]);
	}
	cout<<a;
	return 0;
}
```
