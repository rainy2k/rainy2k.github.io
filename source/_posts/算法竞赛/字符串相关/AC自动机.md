---
title: AC自动机
date: 2022-09-15 11:57:00
tags: 
categories: 
- 数据结构
---

AC自动机
<!--more-->

## 模板
找到有多少个匹配的

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e6 + 100;
const int cc = 26;
#define ms(a,b) memset(a,b,sizeof(a))
struct ACAM{
    int ch[maxn][cc], fail[maxn];
    int end[maxn], idx[maxn];
    int tot;
    void init() {
        ms(ch, 0), ms(end, 0), ms(fail, 0);
        tot = 0;
    }
    void insert(char *s) {
        int u = 0;
        for (int i = 0; s[i];i++) {
            int v = s[i]-'a';
            if(!ch[u][v]) {
                ch[u][v] = ++tot;
            }
            u = ch[u][v];
        }
        end[u]++;
    }

    queue<int> q;
    void build(){
        for (int i = 0; i < cc; i++) {
            if (ch[0][i])
                q.push(ch[0][i]);
        }
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            for (int v = 0; v < cc;v++) {
                if(ch[u][v]) {
                    fail[ch[u][v]] = ch[fail[u]][v];
                    q.push(ch[u][v]);
                }
                else
                    ch[u][v] = ch[fail[u]][v];
            }
        }
    }

    int query(char *t) {
        int u = 0, res = 0;
        for (int i = 0; t[i];i++) {
            int v = t[i] - 'a';
            u = ch[u][v];
            for (int j = u; j && end[j] != -1;j = fail[j]) {
                res += end[j], end[j] = -1;
            }
        }
        return res;
    }
} ac;
char s[maxn];
char t[maxn];
int main() {
    int n;
    while(~scanf("%d", &n)) {
        for (int i = 1; i <= n;i++) {
            scanf("%s", s);
            ac.insert(s);
        }
        ac.build();
        scanf("%s", t);
        cout << ac.query(t) << endl;
    }
}
```

找到匹配次数最多的模板串

https://www.luogu.com.cn/problem/P3796

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 155 * 75;
const int cc = 26;
#define ms(a,b) memset(a,b,sizeof(a))
struct AC{
    int ch[maxn][cc], fail[maxn];
    int idx[maxn], val[maxn], cnt[maxn];
    int tot;
    void init() {
        ms(ch, 0), ms(fail, 0);
        ms(idx, 0), ms(val, 0), ms(cnt, 0);
        tot = 0;
    }
    void insert(char *s,int id) {
        int u = 0;
        for (int i = 0; s[i]; i++) {
            int v = s[i]-'a';
            if(!ch[u][v]) {
                ch[u][v] = ++tot;
            }
            u = ch[u][v];
        }
        idx[id] = u;//如果有重复字符串,并不影响答案
    }

    queue<int> q;
    void build(){
        for (int i = 0; i < 26; i++) {
            if (ch[0][i])
                q.push(ch[0][i]);
        }
        while (q.size()) {
            int u = q.front();
            q.pop();
            for (int v = 0; v < 26;v++) {
                if(ch[u][v]) {
                    fail[ch[u][v]] = ch[fail[u]][v];
                    q.push(ch[u][v]);
                }
                else
                    ch[u][v] = ch[fail[u]][v];
            }
        }
    }

    int query(char *t) { //返回出现的最大次数
        int u = 0, res = 0;
        for (int i = 0; t[i];i++) {
            int v = t[i] - 'a';
            u = ch[u][v];
            for (int j = u; j;j = fail[j]) {
                val[j]++;
            }
        }
        for (int i = 0; i <= tot; i++) {
            if (idx[i]) {
                res = max(res, val[i]);
                cnt[idx[i]] = val[i];
            }
        }
        return res;
    }
} ac;
char s[200][100];
char t[1000005];
int main() {
    int n;
    while(~scanf("%d", &n)&&n!=0) {
        ac.init();
        for (int i = 1; i <= n;i++) {
            scanf("%s", s[i]);
            ac.insert(s[i], i);
        }
        ac.build();
        scanf("%s", t);
        int mx = ac.query(t);
        printf("%d\n", mx);
        for(int i=1;i<=n;i++) {
            if(ac.cnt[i]==mx) {
                printf("%s\n", s[i]);
            }
        }
    }
}
```

输出每个模式串在文本串中出现的次数

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 10;
const int cc = 26;
#define ms(a,b) memset(a,b,sizeof(a))
struct AC{
    int ch[maxn][cc], fail[maxn];
    int idx[maxn], siz[maxn];
    int tot;
    void init() {
        ms(ch, 0), ms(fail, 0);
        ms(idx, 0), ms(siz, 0);
        tot = 0;
    }
    void insert(char *s,int id) {
        int u = 0;
        for (int i = 0; s[i]; i++) {
            int v = s[i]-'a';
            if(!ch[u][v]) {
                ch[u][v] = ++tot;
            }
            u = ch[u][v];
        }
        idx[id] = u;//将第id个模式串对应的节点标记出来(可以重复)
    }

    queue<int> q;
    void build(){
        for (int i = 0; i < 26; i++) {
            if (ch[0][i])
                q.push(ch[0][i]);
        }
        while (q.size()) {
            int u = q.front();
            q.pop();
            for (int v = 0; v < 26;v++) {
                if(ch[u][v]) {
                    fail[ch[u][v]] = ch[fail[u]][v];
                    q.push(ch[u][v]);
                }
                else
                    ch[u][v] = ch[fail[u]][v];
            }
        }
    }

    vector<int> e[maxn];
    void dfs(int u) {
        for(int v:e[u]) {
            dfs(v);
            siz[u] += siz[v];
        }
    }
    void query(char *t) { //计算每个模式串出现的次数
        int u = 0, res = 0;
        for (int i = 0; i <= tot; i++) e[i].clear();
        for (int i = 0; t[i];i++) {
            int v = t[i] - 'a';
            u = ch[u][v];
            siz[u]++;
        }
        for(int i=1;i<=tot;i++) {
            e[fail[i]].push_back(i);//建fail树
        }
        dfs(0);
    }
} ac;
char s[maxn];
char t[2000005];
int main() {
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n; i++) {
        scanf("%s", s);
        ac.insert(s, i);
    }
    ac.build();
    scanf("%s", t);
    ac.query(t);
    for (int i = 1; i <= n; i++) {
        printf("%d\n", ac.siz[ac.idx[i]]);
    }
}
```
## 例题
### 小明系列故事-女友的考验
题意

> 从1号点走到n号点,路径只能按照升序
>
> 给定m条限制,这m条路径是不可以走的
>
> 请问从1到n的最短路径是什么

思路

> 在Tire图中,每一个结点代表一个前缀
>
> 把不可以走的路径放到Tire图里面,这样每个结点代表一个状态,如果那个状态被标记,则代表不可以走,这样在最短路的时候就不可以算这个点
>
> $dp[i][j]$表示在第i个点,状态是j
>
> 具体看代码

代码 : http://xlorpaste.cn/knri9b

注意

```cpp
end[u] |= end[fail[u]];//把危险节点都标记上
```
