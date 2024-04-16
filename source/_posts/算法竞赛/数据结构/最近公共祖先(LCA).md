---
title: 最近公共祖先
date: 2022-09-13 11:57:00
tags: 
categories: 
- 算法
---
# code
```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 100;
int head[maxn], to[maxn * 2], nxt[maxn * 2], d[maxn * 2], tot;
int n, m;
void add(int x, int y, int w){
    to[++tot] = y; nxt[tot] = head[x]; d[tot] = w; head[x] = tot;
}
int dp[maxn][20], dep[maxn], dis[maxn];
void dfs(int u, int fa){
    dp[u][0] = fa; dep[u] = dep[fa] + 1;
    for (int i = head[u]; i; i = nxt[i]) {
        int v = to[i];
        if (v == fa) continue;
        dis[v] = dis[u] + d[i];
        dfs(v, u);
    }
}
void init(){
    memset(dp, 0, sizeof(dp));
    dep[0] = dis[0] = 0;
    dfs(1, 0); 
    for (int j = 1; j < 20; j++) 
        for (int i = 1; i <= n; i++) 
            dp[i][j] = dp[dp[i][j - 1]][j - 1];
}
int qlca(int x, int y) {
    if (dep[x] < dep[y]) swap(x, y);
    int tmp = dep[x] - dep[y];
    for (int i = 0; tmp; i++, tmp >>= 1)
        if (tmp & 1) x = dp[x][i];
    if (x == y) return x;
    for (int i = 19; i >= 0; i--) {
        if (dp[x][i] != dp[y][i]) {
            x = dp[x][i]; y = dp[y][i];
        }
    }
    return dp[x][0];
}
int dist(int x,int y) {
    int u = qlca(x, y);
    int ans =  dis[x] + dis[y] - 2*dis[u];
    return dis[x] + dis[y] - 2*dis[u];
}
```
<!--more-->

# 例题
## [CF1304E]1-Trees and Queries 
https://codeforces.com/contest/1304/problem/E 

给一棵树,每次查询时在x和y之间加一条边,然后问在a和b之间是否存在一条可以重复走的路径且这条路径的长度为k

如果不走x和y的那条连边,那么a到b之间的路径长度为a与b的最短路径+2i,因为重复走的边对答案的贡献一定是偶数长度,只有走了x到y的那条连边,贡献为1,可以改变路径的奇偶性,枚举两种情况即可

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 100;
int head[maxn], to[maxn * 2], nxt[maxn * 2], d[maxn * 2], tot;
int n, m;
void add(int x, int y, int w){
    to[++tot] = y; nxt[tot] = head[x]; d[tot] = w; head[x] = tot;
}
int dp[maxn][20], dep[maxn], dis[maxn];
void dfs(int u, int fa){
    dp[u][0] = fa; dep[u] = dep[fa] + 1;
    for (int i = head[u]; i; i = nxt[i]){
        int v = to[i];
        if (v == fa) continue;
        dis[v] = dis[u] + d[i];
        dfs(v, u);
    }
}
void init(){
    memset(dp, 0, sizeof(dp));
    dep[0] = dis[0] = 0;
    dfs(1, 0); 
    for (int j = 1; j < 20; j++) 
        for (int i = 1; i <= n; i++) 
            dp[i][j] = dp[dp[i][j - 1]][j - 1];
}
int qlca(int x, int y){
    if (dep[x] < dep[y]) swap(x, y);
    int tmp = dep[x] - dep[y];
    for (int i = 0; tmp; i++, tmp >>= 1)
        if (tmp & 1) x = dp[x][i];
    if (x == y) return x;
    for (int i = 19; i >= 0; i--){
        if (dp[x][i] != dp[y][i]){
            x = dp[x][i]; y = dp[y][i];
        }
    }
    return dp[x][0];
}
int dist(int x,int y) {
    int u = qlca(x, y);
    int ans =  dis[x] + dis[y] - 2*dis[u];
    return dis[x] + dis[y] - 2*dis[u];
}
int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n-1;i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        add(x, y, 1);
        add(y, x, 1);
    }
    init();
    int q;
    scanf("%d", &q);
    while(q--) {
        int a, b, x, y,k;
        scanf("%d%d%d%d%d", &x, &y, &a, &b, &k);
        int d = dist(a, b);
        if(k>=d&&(k-d)%2==0) {
            printf("YES\n");
            continue;
        }
        else {
            d = dist(a,x)+dist(b,y)+1;
            if(k>=d&&(k-d)%2==0) {
                printf("YES\n");
                continue;
            }
            d = dist(a,y)+dist(b,x)+1;
            if(k>=d&&(k-d)%2==0) {
                printf("YES\n");
                continue;
            }
        }
        printf("NO\n");
    }
}
```



倍增算法
此算法依然利用了二进制的思想，将O(n)的跳跃优化为O(logn)

f[x,k]表示从x出发，向上跳跃$2^k$步

优美的公式：f[x,k] = f[f[x,k-1],k-1]

解释：$x+2^k = (x+2^{k-1})+2^{k-1}$

为什么倍增是有效的？

```cpp
for (int i = t; i >= 0;i--) { //*从大向小跳
    if(dep[f[y][i]]>=dep[x]) // 先将深度大的向上跳到和深度小的相同的位置
        y = f[y][i];
}
```
比如说，需要跳5步，那么先尝试跳4步，然后再跳1步

5 = (101)

但如果先跳1步，再跳2步，再跳4步,则跳了7步，就需要回溯，因为此处跳2步是不必要的

时间复杂度：$O((n+m)logn)$

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e6 + 100;
vector<int> e[maxn];
int dep[maxn];
int f[maxn][20];
int t;

void bfs(int s) {
    queue<int> q;
    q.push(s);
    dep[s] = 1;
    while(!q.empty()) {
        int u = q.front();
        q.pop();
        for(int v:e[u]) {
            if(dep[v])
                continue;
            dep[v] = dep[u] + 1;
            f[v][0] = u; // 向上跳一(2^0 = 1)步，即跳到自己的父亲处
            for (int i = 1;i<=t;i++) { // *注意这里，需要“转移一下”
                f[v][i] = f[f[v][i - 1]][i - 1];
            }
            q.push(v);
        }
    }
}


int lca(int x,int y) {
    if(dep[x]>dep[y])
        swap(x, y);
    for (int i = t; i >= 0;i--) { //*从大向小跳
        if(dep[f[y][i]]>=dep[x]) // 先将深度大的向上跳到和深度小的相同的位置
            y = f[y][i];
    }
    if(x == y)
        return x;
    for (int i = t; i >= 0;i--) {
        if(f[x][i]!=f[y][i]) { // 两者深度相同，一起往上跳
            x = f[x][i];
            y = f[y][i];
        }
    }
    return f[x][0];
}

int main() {
    int n,m,s;
    cin >> n >> m >> s;
    t = (int)(log(n) / log(2)) + 1;
    for (int i = 1;i<n;i++) {
        int x, y;
        cin >> x >> y;
        e[x].push_back(y);
        e[y].push_back(x);
    }
    bfs(s);
    while(m--) {
        int x,y;
        cin>>x>>y;
        cout << lca(x, y) << endl;
    }
}
```
Tarjan算法
LCA的tarjan算法是离线算法，需要记录所有的查询。

本质上是：使用并查集对“向上标记法”的优化

时间复杂度：$O(n+m)$

通常离线算法在时间复杂度上会比在线算法更优

我们知道在dfs中，未进行回溯的结点分为两种情况：

1.被访问了一次，正在向下进行搜索【标记为1】

2.尚未被访问【标记为0】

将已经回溯过的结点标记为2

考虑这种情况：假设有一个结点x，已经被回溯过，标记为2，结点y正在被访问，标记为1，现在求lca(x,y)。

可以知道，他们的lca现在的标记应该是1，因为一个结点被回溯的前提是，它的所有子节点已经被回溯过！

它们的LCA即为：从x结点向上走到根，第一个遇到的标记为1的结点。

而求这个的过程用并查集来完成

每当一个结点被标记为2的时候，则把它所在的集合合并到它父节点所在的集合里，此时它的父节点标记仍为1，且它的父结点的集合大小为1
查询y所在集合的代表元素，就能查到我们想要的那个“第一个遇到的标记为1的结点”
代码如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e6 + 100;
vector<int> e[maxn];
vector<int> query[maxn];
vector<int> query_id[maxn];

int dep[maxn];
int f[maxn];
int vis[maxn];
int ans[maxn];

int find(int x) {
    return f[x] == x ? x : f[x] = find(f[x]);
}

void tarjan(int u) {
    vis[u] = 1;
    for (int v:e[u]) {
        if(vis[v])
            continue;
        tarjan(v);
        f[v] = u;
    }
    for (int i = 0; i < query[u].size();i++) {
        int v = query[u][i];
        int id = query_id[u][i];
        if(vis[v] == 2) { //当前vis[u] = 1,vis[v] = 2;
            // 它们的lca是v向上出发遇到的第一个标记为1的结点
            ans[id] = find(v);
        }
    }
    vis[u] = 2; // 表示u结点已经被回溯过了
}


int main() {
    int n,m,s;
    cin >> n >> m >> s;
    for (int i = 1;i<n;i++) {
        int x, y;
        cin >> x >> y;
        e[x].push_back(y);
        e[y].push_back(x);
    }
    for (int i = 1;i<=n;i++) {
        f[i] = i;
    }
    for (int i = 1; i <= m; i++) {
        int x, y;
        cin >> x >> y;
        if(x == y) {
            ans[i] = x;
            continue;
        }
        query[x].push_back(y);
        query[y].push_back(x);
        query_id[x].push_back(i);
        query_id[y].push_back(i);
    }
    tarjan(s);
    for (int i = 1;i<=m;i++) {
        cout << ans[i] << endl;
    }
}
```
同时，我们知道，求书上两点之间的距离也是同理的

ans = dis[x]+dis[y] - 2*dis[lca(x,y)]

其中d[x]是从根结点到x的最短距离