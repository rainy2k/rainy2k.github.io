---
title: 树形DP
date: 2022-09-13 11:57:00
tags: 
categories: 
---

# 树形DP
## Tree painting
```cpp
#include <bits/stdc++.h>
#include<stdint.h>
#define int long long
#define scan(n) scanf("%lld", &(n))
#define scann(n, m) scanf("%lld%lld", &(n), &(m))
#define scannn(a, b, c) scanf("%lld%lld%lld", &(a), &(b), &(c))
#define prin(n) printf("%lld", (n))
#define pb push_back
#define mp make_pair
#define ms(a) memset(a, 0, sizeof(a))
#define fo(i, a, b) for (int i = (a); i <= (b); i++)
#define ro(i, a, b) for (int i = (a); i >= (b); i--)
const int inf = 0x3f3f3f3f;
using namespace std;
const int maxn = 2e5+100;
vector<int>e[maxn];
int siz[maxn];
int ans,n;
int u,v;
void getsize(int v,int u){
    siz[v]=1;
    for(int x:e[v]){
        if(x==u)continue;
        getsize(x,v);
        siz[v]+=siz[x];
    }
}
void dfs(int v,int u,int t){
    ans=max(ans,t);
    cout<<v<<" "<<u<<" "<<t<<endl;
    for(int x:e[v]){
        if(x==u)continue;
        dfs(x,v,t-2*siz[x]+n);
    }
}
int32_t main() {
    scan(n);
    fo(i,1,n-1){
        scann(u,v);
        e[u].pb(v);e[v].pb(u);
    }
    getsize(1,0);
    int t=0;
    fo(i,1,n)t+=siz[i];
    dfs(1,0,t);
    printf("%lld",ans);
    return 0;
}
```
<!--more-->

## [CF816E] 树上背包
注意最后求答案的时候一定是$dp[1][i][0]$或者是$dp[1][i][1]$
因为想要使用优惠券,就必须保证它们的父亲被选了,而点1就是根节点
``` cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
const int inf = 1e15;
const int maxn = 5000 + 10;
int n, b;
int c[maxn], d[maxn], x[maxn];
int dp[maxn][maxn][2];
int sz[maxn];
vector<int> G[maxn];
void dfs(int u)
{
    sz[u] = 1;
    dp[u][1][0] = c[u];
    dp[u][1][1] = c[u] - d[u];
    dp[u][0][0] = 0;
    for(int v:G[u]){
        dfs(v);
        for (int i = sz[u]; i >= 0; i--)
        {
            for (int j = 0; j <= sz[v];j++)
            {
                dp[u][i + j][0] = min(dp[u][i + j][0], dp[u][i][0] + dp[v][j][0]);
                dp[u][i + j][1] = min(dp[u][i + j][1], dp[u][i][1] + dp[v][j][0]);
                dp[u][i + j][1] = min(dp[u][i + j][1], dp[u][i][1] + dp[v][j][1]);
            }
        }
        sz[u] += sz[v];
    }
}
int32_t main()
{
    scanf("%lld%lld", &n, &b);
    scanf("%lld%lld", &c[1], &d[1]);
    for (int i = 2; i <= n;i++)
    {
        scanf("%lld%lld%lld", &c[i], &d[i], &x[i]);
        G[x[i]].push_back(i);
    }
    for (int i = 0; i <= n;i++)
        for (int j = 0; j <= n;j++)
            dp[i][j][0] = dp[i][j][1] = inf;
    dfs(1);
    int ans = 0;
        for (int j = 1; j <= n; j++)
            if(dp[1][j][0]<=b||dp[1][j][1]<=b)
                ans = max(ans, j);
    cout << ans << endl;
}
```

## [CF1324E] Maximum White Subtree 
```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 100;

vector<int> e[maxn];
int n;
int a[maxn], ans[maxn], d[maxn];
void dfs(int u,int fa) {
    d[u] = a[u];
    for(int v:e[u]) {
        if(v==fa)
            continue;
        dfs(v, u);
        if(d[v]>0) {
            d[u] += d[v];
        }
    }
    return;
}

void dfs2(int u,int fa) {
    ans[u] = d[u];
    for(int v:e[u]) {
        if(v==fa)
            continue;
        //换根操作
        d[u] -= max(0, d[v]);//只有d[v]大于0时,v才可能对u有贡献
        d[v] += max(0, d[u]);//d[u]大于0对v才有贡献
        dfs2(v, u);
        d[v] -= max(0, d[u]);
        d[u] += max(0, d[v]);
    }
}


int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n;i++) {
        scanf("%d", &a[i]);
        if(a[i]==0)
            a[i] = -1;
    }
    for (int i = 1; i <= n - 1;i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        e[x].push_back(y);
        e[y].push_back(x);
    }
    dfs(1, 0);
    dfs2(1, 0);
    for (int i = 1; i <= n;i++) {
        printf("%d ", ans[i]);
    }
}
```

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 100;

vector<int> e[maxn];
int n;
int a[maxn], ans[maxn], d[maxn];
void dfs(int u,int fa) {
    d[u] = a[u];
    for(int v:e[u]) {
        if(v==fa)
            continue;
        dfs(v, u);
        if(d[v]>0) {
            d[u] += d[v];
        }
    }
    return;
}

void dfs2(int u,int fa) {
    ans[u] = d[u];
    for(int v:e[u]) {
        if(v==fa)
            continue;
        int t = d[u];
        if(d[v]>0)
            t -= d[v];
        if(t>0)
            d[v] += t;
        dfs2(v, u);
    }
}


int main() {
    scanf("%d", &n);
    for (int i = 1; i <= n;i++) {
        scanf("%d", &a[i]);
        if(a[i]==0)
            a[i] = -1;
    }
    for (int i = 1; i <= n - 1;i++) {
        int x, y;
        scanf("%d%d", &x, &y);
        e[x].push_back(y);
        e[y].push_back(x);
    }
    dfs(1, 0);
    dfs2(1, 0);
    for (int i = 1; i <= n;i++) {
        printf("%d ", ans[i]);
    }
}
```
