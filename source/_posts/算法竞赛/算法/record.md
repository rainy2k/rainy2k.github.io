---
title: Record
date: 2022-09-15 11:57:00
tags:
categories: 
- 算法
---

一些题目的记录
<!--more-->

# Recursive sequence矩阵快速幂
```c++
#include <bits/stdc++.h>
#include<stdint.h>
#define int long long
#define scan(n) scanf("%lld", &(n))
#define scann(n, m) scanf("%lld%lld", &(n), &(m))
#define scannn(a, b, c) scanf("%lld%lld%lld", &(a), &(b), &(c))
#define pb push_back
#define fo(i, a, b) for (int i = (a); i <= (b); i++)
const int N=8;//N个系数，N维矩阵
using namespace std;
const int mod=2147493647;
struct matrix{int m[10][10];};
matrix ans,base,m;
int n,a,b;
matrix multi(matrix a, matrix b){
    matrix tmp;
    for(int i=1;i<=N;++i){
        for (int j=1;j<=N;++j){
            tmp.m[i][j]=0;
            for(int k=1;k<=N;++k)
                tmp.m[i][j]=(tmp.m[i][j]+(a.m[i][k]*b.m[k][j]))%mod;
        }
    }
    return tmp;
}
matrix fastm(matrix base, int n){
    matrix ans;
    memset(ans.m,0,sizeof(ans.m));
    for (int i=1;i<=N;i++)ans.m[i][i]=1;
    while(n){
        if(n&1)ans=multi(ans,base);
        base=multi(base, base);
        n>>=1;
    }
    return ans;
}
int32_t main(){
    matrix m,ans;
    memset(m.m,0,sizeof(m.m));
    m.m[1][1]=1;m.m[1][2]=2;m.m[1][4]=1;m.m[1][5]=4;m.m[1][6]=6;m.m[1][7]=4;m.m[1][8]=1;
    m.m[2][1]=1;
    m.m[3][2]=1;
    m.m[4][4]=1;m.m[4][5]=4;m.m[4][6]=6;m.m[4][7]=4;m.m[4][8]=1;
    m.m[5][5]=1;m.m[5][6]=3;m.m[5][7]=3;m.m[5][8]=1;
    m.m[6][6]=1;m.m[6][7]=2;m.m[6][8]=1;
    m.m[7][7]=1;m.m[7][8]=1;
    m.m[8][8]=1;
    int T;scan(T);
    while(T--){
        scannn(n,a,b);
        ans = fastm(m,n-3);
        int f[10],x=0;
        f[3]=a,f[2]=b,f[1]=(2*a+b+3*3*3*3),f[4]=3*3*3*3,f[5]=3*3*3,f[6]=3*3,f[7]=3,f[8]=1;
        fo(i,1,8){
            x=x%mod+((f[i]%mod)*(ans.m[1][i])%mod)%mod;
            x%=mod;
        }
        printf("%lld\n",x);
    }
    return 0;
```
矩阵快速幂，用于求解递推式。
<!--more-->

## Happy Necklace 矩阵快速幂

注意**状态的转移**，很有意思~

```cpp
#include <bits/stdc++.h>
#include<stdint.h>
using namespace std;
#define int long long
#define scan(n) scanf("%lld", &(n))
#define ms(a) memset(a, 0, sizeof(a))
#define fo(i, a, b) for (int i = (a); i <= (b); i++)
#define ro(i, a, b) for (int i = (a); i >= (b); i--)
#define dbg(args...) do {cout << #args << " : "<< args << endl;} 
const int inf = 0x3f3f3f3f;
const int mod=1e9+7;
const int N=3;
struct matrix{
    int m[N+1][N+1];
};
matrix e,d;
matrix qpow(matrix a,int b){
    matrix ans=e;
    while(b){
        if(b&1){
            matrix x=d;
            for(int i=0;i<=N-1;i++)
                for(int j=0;j<=N-1;j++)
                    for(int k=0;k<=N-1;k++)
                        x.m[i][j]=x.m[i][j]%mod+ans.m[i][k]*a.m[k][j]%mod;
            ans=x;
        }
        matrix x=d;
        for(int i=0;i<=N-1;i++)
            for(int j=0;j<=N-1;j++)
                for(int k=0;k<=N-1;k++)
                    x.m[i][j]=x.m[i][j]%mod+a.m[i][k]*a.m[k][j]%mod;
        a=x;
        b>>=1;
    }
    return ans;
}

int32_t main(){
    int T;scan(T);
    matrix base,m;
    for(int i=0;i<=N-1;i++){
        for(int j=0;j<=N-1;j++){
            base.m[i][j]=0;
            m.m[i][j]=0;
            e.m[i][j]=0;
            d.m[i][j]=0;
        }
    }
    base.m[0][0]=1;
    base.m[1][0]=1;
    base.m[2][0]=1;
    m.m[0][0]=1;m.m[0][1]=0;m.m[0][2]=1;
    m.m[1][0]=1;m.m[1][1]=0;m.m[1][2]=0;
    m.m[2][0]=0;m.m[2][1]=1;m.m[2][2]=0;
    for(int i=0;i<=N-1;i++)e.m[i][i]=1;
    while(T--){
        int n;scan(n);
        int ans=0;
        if(n==1)ans=2;
        else if(n==2)ans=3;
        else{
            matrix temp=qpow(m,n-2);
            matrix x=d;
            for(int i=0;i<=N-1;i++)
                for(int j=0;j<=N-1;j++)
                    for(int k=0;k<=N-1;k++)
                        x.m[i][j]=x.m[i][j]%mod+temp.m[i][k]*base.m[k][j]%mod;
            temp=x;
            ans=temp.m[0][0]+temp.m[1][0]+temp.m[2][0];
            ans%=mod;
        }
        printf("%lld\n",ans);
    }
}
```



## Counting cliques DFS


```cpp
include <bits/stdc++.h>
include<stdint.h>
using namespace std;
define int long long
define scan(n) scanf("%lld", &(n))
define scann(n, m) scanf("%lld%lld", &(n), &(m))
define scannn(a, b, c) scanf("%lld%lld%lld", &(a), &(b), &(c))
define pb push_back
define fo(i, a, b) for (int i = (a); i <= (b); i++)
const int inf = 0x3f3f3f3f;
const int maxn = 2e5+100;
int n,m,s,x,y,pre[20],e120,ans,tot,head[120];
struct node{
    int v,nxt;
}a[1024];
void add(int u,int v){
    tot++;
    a[tot].v=v;
    a[tot].nxt=head[u];
    head[u]=tot;
}
void dfs(int u,int step){
    pre[step]=u;
    if(step==s){ans++;return;}
    for(int j=head[u];~j;j=a[j].nxt){
        int x=a[j].v;
        int f=1;
        fo(i,1,step){
            if(epre[i]==0){f=0;break;}
        }
        if(f){
            step++;
            dfs(x,step);
            step--;
        }
    }
    return;
}
int32_t main() {
    int T;scan(T);
    while(T--){
        scannn(n,m,s);
        fo(i,0,15)pre[i]=0;
        fo(i,0,105)fo(j,0,105)ei=0;
        fo(i,0,105)head[i]=-1;
        ans=0,tot=0;
        fo(i,1,m){
            scann(x,y);
            if(x>y)swap(x,y);
            ex=1;add(x,y);
        }
        fo(i,1,n)dfs(i,1);
        printf("%lld\n",ans);
    }
    return 0;
}
```



## Distance in Tree 

[题目链接](http://codeforces.com/contest/161/problem/D )

## Tree painting 树形DP


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

## ABOUT 笛卡尔树

## 单调栈

<http://www.hankcs.com/program/algorithm/poj-3250-bad-hair-day.html>

<https://blog.csdn.net/wubaizhe/article/details/70136174>

<https://ac.nowcoder.com/acm/contest/view-submission?submissionId=40898761>

<https://blog.csdn.net/u013554860/article/details/51360542>

<https://xlor.cn/2019/4/2019nanchang/>

https://blog.csdn.net/lr7682/article/details/90247997

## [牛客第一场AEquivalent Prefixes-前缀笛卡尔树](https://ac.nowcoder.com/acm/contest/881/A )

序列u，v 对于$[1,ans]$上所有的$[L,R]$$(1<=L<=R<=ans<=n)$

都满足$RMQ(u,L,R)=RMQ(v,L,R)$

求$max(ans)$;

分析：考虑判断两个序列的前缀笛卡尔树是否相等

注意，如果前缀笛卡尔树相等，则可以判断每一个栈深都相同，想想为什么？、


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
const int maxn = 1e5+100;
int n;
int a[maxn],b[maxn];
int s1[maxn],s2[maxn];
int32_t main() {
    while(scan(n)!=EOF){
        fo(i,1,n)scan(a[i]);
        fo(i,1,n)scan(b[i]);
        int head1=1,head2=1,tail1=1,tail2=1;
        s1[head1]=s2[head2]=inf;
        int maxpos=1;
        fo(i,1,n){
            while(head1<=tail1&&s1[tail1]>a[i])tail1--;
            while(head2<=tail2&&s2[tail2]>b[i])tail2--;
            //cout<<"before:\n";
            //cout<<"tail1: "<<tail1<<" s1[tail1]:"<<s1[tail1]<<endl;
            //cout<<"tail2: "<<tail2<<" s2[tail2]:"<<s2[tail2]<<endl;
            if(tail1!=tail2)break;
            s1[++tail1]=a[i],s2[++tail2]=b[i];
            //cout<<"after:\n";
            //cout<<"tail1: "<<tail1<<" s1[tail1]:"<<s1[tail1]<<endl;
            //cout<<"tail2: "<<tail2<<" s2[tail2]:"<<s2[tail2]<<endl;
            maxpos=i;
        }
        cout<<maxpos<<endl;
    }
    return 0;
}
```

## 笛卡尔树习题

http://acm.hdu.edu.cn/showproblem.php?pid=6305

https://blog.csdn.net/zhaiqiming2010/article/details/80245872



##[CF1199FRectangle Painting 1-区间DP](https://codeforces.com/contest/1199/problem/F )

给定一个$n \times n$的矩阵,由$"\#”,"."$组成，每次可以将$h\times w$矩形内的$"\#"$变成$"."$

花费为$max(h,w)$,求将所有$"\#"$变成$"."$的最小花费


```cpp
#include<bits/stdc++.h>
#define fo(i,a,b) for(int i=a;i<=b;i++)
using namespace std;
char a[55][55];
int f[55][55][55][55];
int dp(int x,int y,int xx,int yy){
    if(f[x][y][xx][yy]!=-1)return f[x][y][xx][yy];
    int ans=max(xx-x+1,yy-y+1);
    fo(i,x,xx-1)ans=min(ans,dp(x,y,i,yy)+dp(i+1,y,xx,yy));
    fo(i,y,yy-1)ans=min(ans,dp(x,y,xx,i)+dp(x,i+1,xx,yy));
    return f[x][y][xx][yy]=ans;
}
int main(){
    int n;scanf("%lld",&n);
    fo(i,1,n)scanf("%s",a[i]+1);
    memset(f,-1,sizeof(f));
    fo(x,1,n){
        fo(y,1,n){
            fo(xx,1,n){
                fo(yy,1,n){
                    if(x>xx||y>yy)f[x][y][xx][yy]=0;
                    if(x==xx&&y==yy)f[x][y][xx][yy]=(a[x][y]=='#'?1:0);
                }
            }
        }
    }
    cout<<dp(1,1,n,n)<<endl;
    
}
```



##[CF1190EMatching vs Independent Set ](https://codeforces.com/contest/1199/problem/E ) 图匹配

给定一个3*n个点m条边的简单无向图，要求在这个图里找出一个有n条边的连通图

或者找出一个有n个点的独立点集，并且输出答案


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
const int maxn = 3e5+100;
int n,m;
int u[maxn],v[maxn];
int vis[maxn];
int32_t main() {
    int T;scan(T);
    while(T--){
        scann(n,m);
        fo(i,1,3*n)vis[i]=0;
        vector<int>edge_ans;
        fo(i,1,m){
            int u,v;scann(u,v);
            if(vis[u]||vis[v])continue;
            vis[u]=1,vis[v]=1;
            edge_ans.pb(i);
        }
        if(edge_ans.size()>=n){
            printf("Matching\n");
            fo(i,0,n-1){
                printf("%lld ",edge_ans[i]);
            }
        }
        else{
            printf("IndSet\n");
            int cnt=0;
            fo(i,1,3*n){
                if(cnt>=n)break;
                if(!vis[i])printf("%lld ",i),cnt++;
            }
        }
        printf("\n");
    }
    return 0;
}
```



## 盒子-组合数

隔板法；

子问题：给定n个物品，将它们分成m堆，保证每堆的物品个数不少于h个

则有$C_{n-m*h-1}^{m-1}$种分割方法

```cpp
#include <bits/stdc++.h>
#include<stdint.h>
using namespace std;
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
#define dbg(args...) do {cout << #args << " : "<< args << endl;} 
const int inf = 0x3f3f3f3f;
const int maxn = 1e5+100;
const int mod=1e9+7;

int f[maxn],inv[maxn];
int qpow(int a,int b){
    int res=1;
    while(b){
        if(b&1)res=res*a%mod;
        a=a*a%mod;;
        b/=2;
    }
    return res;
}
void init(){
    f[0]=1;
    for(int i=1;i<=maxn-1;i++)f[i]=f[i-1]*i%mod;
    inv[maxn-1]=qpow(f[maxn-1],mod-2);
    for(int i=maxn-2;i>=0;i--)inv[i]=inv[i+1]*(i+1)%mod;
}
int C(int n,int m){
    return f[n]*inv[m]%mod*inv[n-m]%mod;
}
int32_t main() {
    int f,w,h;
    cin>>f>>w>>h;
    int fm=0,fz=0;
    init();
    for(int i=1;i<=w;i++){
        fm+=(C(w-1,i-1)*C(f+1,i))%mod;
        fm%=mod;
    }
    for(int i=1;i<=w/(h+1);i++){
        fz=fz+C(w-i*h-1,i-1)*C(f+1,i)%mod;
        fz%=mod;
    }
    int ans=fz*qpow(fm,mod-2)%mod;
    cout<<ans<<endl;
    return 0;
}

```

组合数部分：

```cpp
for(int i=1;i<=w;i++){
        fm=fm+(C(w-1,i-1)*C(f-1,i-1))*2%mod;
        fm=fm+(C(w-1,i-1)*C(f-1,i-2))%mod;
        fm=fm+(C(w-1,i-1)*C(f-1,i))%mod;
        fm%=mod;
    }
    for(int i=1;i<=w/(h+1);i++){
        fz=fz+C(w-i*h-1,i-1)*C(f-1,i-1)*2%mod;
        fz=fz+C(w-i*h-1,i-1)*C(f-1,i-2)%mod;
        fz=fz+C(w-i*h-1,i-1)*C(f-1,i)%mod;
        fz%=mod;
    }

```

可以合并为：

```cpp
    for(int i=1;i<=w;i++){
        fm+=(C(w-1,i-1)*C(f+1,i))%mod;
        fm%=mod;
    }
    for(int i=1;i<=w/(h+1);i++){
        fz=fz+C(w-i*h-1,i-1)*C(f+1,i)%mod;
        fz%=mod;
    }

```





## 找朋友-状压DP

赵队出的题，找朋友

```cpp
#include <bits/stdc++.h>
#include<stdint.h>
using namespace std;
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
#define dbg(args...) do {cout << #args << " : "<< args << endl;} 
const int inf = 0x3f3f3f3f;
const int maxn = (1<<21);
int x[25],y[25];
int n,f;
double dp[maxn];
bool check(int s,int t){
    for(int i=1;i<=n;i++){
        int x=(1<<i);
        if((!(s&x))&&(t&x))return false;
    }
    return true;
}
double dis(int i,int j){
    return sqrt((x[i]-x[j])*(x[i]-x[j])+(y[i]-y[j])*(y[i]-y[j]));
}
double min_dis(int t,int j){
    vector<double>v;
    for(int i=1;i<=n;i++){
        int x=(1<<i);
        if(t&x){
            v.pb(dis(i,j));
        }
    }
    sort(v.begin(),v.end());
    if(v.size()<=1)return inf;
    return v[0]+v[1];
}
int32_t main() {
    int T;scan(T);
    while(T--){
        scan(n);
        int s=0;
        int xx=((1<<(n+1))-1)^1;
        for(int i=0;i<=maxn-1;i++)dp[i]=inf;
        fo(i,1,n){
            scannn(x[i],y[i],f);
            if(f)s^=(1<<i);
        }
        dp[s]=0;
        for(int i=s;i<=xx;i++){
            if(!check(i,s))continue;
            fo(j,1,n){
                int t=(1<<j);
                if((i&t)&&(!(s&t))){
                    int x=i^t;
                    dp[i]=min(dp[x]+min_dis(x,j),dp[i]);
                }
                else continue;
            }
        }
        if(dp[xx]==inf){
            cout<<"No Solution\n";
            continue;
        }
        printf("%.6lf\n",dp[xx]);
    }
    return 0;
}

```



## 找质数-素数筛

因为数字范围$<=1e14$,所以在快速幂的过程中会爆long long，要用__int128

也可以结合费马小定理 $a^{p-1} \  mod \ p == 1$,P为质数，已知质数间隔小于200

```cpp
#include <bits/stdc++.h>
#include<stdint.h>
using namespace std;
const int maxn=1e7;
__int128 read(){
	__int128 x=0,f=1;
    char ch=getchar();
    while(ch<'0'||ch>'9'){
        if(ch=='-')f=-1;
        ch=getchar();
    }
    while(ch>='0'&&ch<='9'){
        x=x*10+ch-'0';
        ch=getchar();
    }
    return x*f;
}
void print(__int128 x){/*__int128输出*/
    if(x<0)putchar('-'),x=-x;
    if(x>9)print(x/10);
    putchar(x%10+'0');
}
struct Prime{
    int prime_cnt;
    int prime[maxn+5],vis[maxn+5];
    int p[maxn+5];
    void Prime_init(){/*将合数标记为vis[i]=1*/
        prime_cnt=0;
        for(int i=2;i<=maxn;i++){
            if(!vis[i])prime[++prime[0]]=i;
            for(int j=1;j<=prime[0]&&i*prime[j]<=maxn;j++){
                vis[i*prime[j]]=1;
                if(i%prime[j]==0)break;
            }   
        }
        /*prime_cnt记录素数的数量*//*p数字记录每个素数*/
        for(int i=2;i<maxn;i++)if(!vis[i])p[++prime_cnt]=i;
    }
    bool isprime(__int128 x){
        for(int i=1;i<=prime_cnt;i++){
            if(p[i]>=x)return true;
            if(x%p[i]==0)return false;
        }
        return true;
    }
}F;
__int128 qpow(__int128 a,__int128 b,__int128 mod){/*快速幂*/
    __int128 res=1;
    while(b){
        if(b&1)res=res*a%mod;
        a=a*a%mod;
        b>>=1;
    }
    return res;
}
int32_t main() {
    int T;cin>>T;
    F.Prime_init();
    while(T--){
        __int128 a,p;p=read(),a=read();
        for(__int128 q=p-1;q>=1;q--){
            if(F.isprime(q)){
                __int128 ans=qpow(a,q,p);
                print(ans);
                printf("\n");
                break;
            }
        }
    }
    return 0;
}

```



## Codeforces204E KMP

给定一主串,再查询某字符串是否可以由主串的两个子串拼接而成.

方法是顺着kmp一遍,再反着kmp一遍,记录查询的字符串的前缀和后缀在主串中的位置的最小值

``` cpp
#include<bits/stdc++.h>
 
using namespace std;
const int maxn = 1e5 + 10005;
int z[maxn];
int pi[maxn];
int pos1[1005], pos2[1005];
int ans;
string s, t;
void prefix(string s) {
    int n = s.size();
    for (int i = 0; i < n;i++)
        pi[i] = 0;
    for (int i = 1; i < n; i++) 
    {
        int j = pi[i - 1];
        while (j > 0 && s[i] != s[j]) j = pi[j - 1];
        if (s[i] == s[j]) j++;
        pi[i] = j;
    }
}

void kmp(string s,string t)//s是待匹配的
{
    for (int i = 0; i <= 1000;i++)
        pos1[i] = pos2[i] = maxn + 5;
    int n = s.size();
    int m = t.size();
    string ss = s + "#" + t;
    prefix(ss);
    for (int i = n + 1; i <= m + n; i++)
    {
        int t = pi[i];
        pos1[t] = min(pos1[t], i - n);
    }
    ss = s + "#" + t;
    prefix(ss);
    for (int i = n + 1; i <= m + n; i++)
    {
        int t = pi[i];
        pos2[t] = min(pos2[t], i - n);
    }
    for (int i = 1; i <= n-1;i++)
    {
        int x = pos1[i];
        int y = pos2[n - i];
        if(x<=m-y){
            ans++;
            break;
        }
    }
}
int main()
{
    cin >> t;
    int q;
    scanf("%d", &q);
    for (int i = 0; i < q;i++)
    {
        cin >> s;
        kmp(s, t);
    }
    cout << ans << endl;
}

```



## Codeforces816E 树上背包

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

## NowCoder 非递归实现组合型枚举

模拟汇编的操作

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e5 + 100;
vector<int> v;
int n, m;
int st[maxn], address, top;
/*
void cal(int x){//参数x表示当前选择的数字
    if(v.size()>m||v.size()+(n-x+1)<m)
        return;
    if(x==n+1){
        for (int i = 0; i < v.size();i++){
            printf("%d ", v[i]);
        }
        printf("\n");
        return;
    }
    //选x
    v.push_back(x);
    cal(x + 1);
    //不选x
    v.pop_back();
    cal(x + 1);
    return;
}*/

void cal(int x,int ret_addr){//新指令(数字)进栈
    int old_top = top;
    st[++top] = x;
    st[++top] = ret_addr;
    st[++top] = old_top;
}
int ret(){
    int ret_addr = st[top - 1];
    top = st[top];//让最后一条语句出栈
    return ret_addr;
}
int main(){
    cin >> n >> m;
    cal(1, 0);//数字1进栈
    while(top){
        int x = st[top - 2]; //获取的是数字x
        switch(address){
            case 0:
                if(v.size()>m||v.size()+(n-x+1)<m){
                    address = ret(); //也就是return;
                    continue;
                }
                if(x==n+1){
                    for (int i = 0; i < v.size();i++){
                        printf("%d ", v[i]);
                    }
                    printf("\n");
                    address = ret();
                    continue;
                }
                v.push_back(x);
                cal(x + 1, 1);
                address = 0;
                continue;
            case 1:
                v.pop_back();
                cal(x + 1, 2);//返回后从cas2继续执行
                address = 0;
                continue;
            case 2:
                address = ret();
        }
    }
}

```

 



## CCPC秦皇岛-KMP

把原串反过来...而已QuQ

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e7 + 5;
char s[maxn], t[maxn];
vector<int> prefix_function(int n){
    vector<int> pi(n);
    for (int i = 1; i < n;i++){
        int j = pi[i - 1];
        while(j>0&&t[i]!=t[j])j = pi[j - 1];
        if(t[i]==t[j])j++;
        pi[i] = j;
    }
    return pi;
}
int main(){
    int a, b;
    while(cin>>a>>b){
        scanf("%s", s);
        long long ans = 1e9 * 1e7 * 2;
        ans = -ans;
        int sz = strlen(s);
        int l = 0;
        for (int i = sz - 1; i >=0; i--)
        {
            if(s[i]=='.')break;
            t[l++] = s[i];
        }
        vector<int> pi = prefix_function(l);
        for (int i = 0; i < l; i++)
        {
            long long pos = i + 1;
            long long len = i + 1;
            len = pos - pi[i];
            ans = max(ans, a * pos - b * len);
        }
        cout << ans << endl;
    }
}

```





## Tree-DP

```CPP
void dp(int u)
{
    sz[u] = 1;
    init();
    f[u][1] = val[x];
    for(int v:to[u])
    {
        dp(v);
        for (int i = sz[u];i>0;i--)
        {
            for (int j = 1; j <= sz[u];j++)
            {
                f[u][i + j] = max(f[u][i + j], f[u][i] + f[v][j]);
            }
        }
        sz[u] += sz[v];
    }
}

void dp(int u)
{
    for(int v:to[u])
    {
        for (int i = 1; i <= m;i++)
            f[v][i] = f[u][i];
        dp(v);
        for (int i = weight[v]; i <= m;i++)
            f[u][i] = max(f[u][i], f[v][i - weight[v]] + value[v]);
    }
}



```





## 点分治

> 树,n个点,边带权,求问边权和小于k的路径条数

对于指定的根节点p,树上的路径分为:

1.经过p       (x ~ p)+(p ~ y )

2.包含于p的一棵子树,且不经过p(递归处理)





## HDU2222 AC自动机

```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 1e6 + 5;

struct Trie {
    int nxt[maxn][26], fail[maxn], end[maxn];
    int root ,L;
    int newnode(){
        for (int i = 0; i < 26; i++)
            nxt[L][i] = -1;
        end[L++] = 0;
        return L - 1;//返回节点的数量
    }
    void init() {
        L = 0;
        root = newnode();
    }

    void insert(char buf[]) {
        int len = strlen(buf);
        int now = root;
        for (int i = 0; i < len; i++)
        {
            int c = buf[i] - 'a';
            if(nxt[now][c]==-1){
                nxt[now][c] = newnode();
            }
            now = nxt[now][c];
        }
        end[now]++;
    }

    void build(){
        queue<int> q;
        fail[root] = root;
        for(int i=0;i<26;i++) {
            if (nxt[root][i] == -1) {
                nxt[root][i] = root;//empty,指向根
            }
            else{
                fail[nxt[root][i]] = root;
                q.push(nxt[root][i]);
            }
        }
        while(!q.empty()) {
            int now = q.front();
            q.pop();
            for(int i=0;i<26;i++) {
                if(nxt[now][i] == -1){
                    nxt[now][i] = nxt[fail[now]][i];
                }
                else{
                    fail[nxt[now][i]] = nxt[fail[now]][i];
                    q.push(nxt[now][i]);
                }
            }
        }
    }

    int query(char buf[]) {
        int len = strlen(buf);
        int now = root;
        int res = 0;
        for(int i=0;i<len;i++) {
            char c = buf[i] - 'a';
            now = nxt[now][c];
            int temp = now;
            while(temp!=root) {
                res += end[temp];
                end[temp] = 0;
                temp = fail[temp];
            }
        }
        return res;
    }
};
char buf[maxn*2];
Trie ac;

int main(){
    int T;
    scanf("%d",&T);
    while(T--) {
        int n;
        scanf("%d", &n);
        ac.init();
        for(int i=0;i<n;i++) {
            scanf("%s", buf);
            ac.insert(buf);
        }
        ac.build();
        scanf("%s",buf);
        printf("%d\n", ac.query(buf));
    }
    return 0;
}

```





## Bitset

```cpp
#include<bits/stdc++.h>
using namespace std;

bitset<5> b, a;
int main() {
    cout << b << endl; //00000

    b.set(2);
    cout << b << endl;//00100

    a.set(3);
    cout << a << endl;//01000

    b ^= a;//(00100)^(01000)
    cout << b << endl;//01100

    b.reset(3);//00100
    cout << b << endl;

    b.flip();//11011
    cout << b << endl;
}

```





## 最大子段和

```cpp
int best=0,sum=0;
for(int i=0;i<n;i++)
{
    sum=max(array[i],sum+array[k]);
    best=max(sun,best);
}
cout<<best<<endl;

```



## Codeforces977E dfs判环

187ms 用dfs找连通块,并且需要环上的每个点的度为2

```cpp
#include<bits/stdc++.h>

using namespace std;
const int maxn = 2e5 + 5;

int vis[maxn];
int ans, f;
vector<int> e[maxn];

void dfs(int u) {   //遍历每个联通块
    vis[u] = 1;
    if(e[u].size()!=2)
        f = 1;
    for(int v : e[u]) {
        if(!vis[v])
            dfs(v);
    }
}

int main() {
    int n, m;
    scanf("%d%d",&n,&m);
    for (int i = 1; i <= m;i++) {
        int u,v;
        scanf("%d%d",&u,&v);
        e[v].push_back(u);
        e[u].push_back(v);
    }
    for(int i=1;i<=n;i++) {
        f = 0;
        if(!vis[i]) {
            dfs(i);
            if(f==0)ans++;
        }
    }
    printf("%d\n", ans);
}

```



93ms 并查集

```cpp
#include <bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 5;

int f[maxn];
int d[maxn];

struct edge{
    int u, v;
}e[maxn];

int find(int x) {
    return f[x] == x ? x : f[x] = find(f[x]);
} 

int main() {
    int n,m;scanf("%d%d",&n,&m);
    for(int i=0;i<=n;i++)
        f[i] = i;
    for(int i=1;i<=m;i++) {
        scanf("%d%d", &e[i].u, &e[i].v);
        d[e[i].u]++;
        d[e[i].v]++;
    }
    int ans = 0;
    for(int i=1;i<=m;i++) {
        if(d[e[i].u]==2&&d[e[i].v]==2) {
            int a = find(e[i].u);
            int b = find(e[i].v);
            if(a==b)
                ans++;
            else if(a<b)
                f[b] = a;
            else
                f[a] = b;
        }
    }
    cout << ans << endl;
}

```





## Codeforces[DP] k-Tree

寒假手速赛第一场

题意:...

时间复杂度:$O(n*k)$

思路:直接模拟，选或者不选，dp数组第一维为和，第二维为是否包含重量大于d的边

（考虑组合数做法？）

 https://codeforces.com/group/GlRm4CeuZ9/contest/266331/problem/C 

```cpp
#include<bits/stdc++.h>
using namespace std;
const long long mod = 1e9 + 7;
const int maxn = 1e5 + 5;
long long dp[maxn][3];
int main() {
    int n, k, d;
    scanf("%d%d%d", &n, &k, &d);
    dp[0][0] = 1;
    for (int i = 1; i <= n;i++) {
        for (int j = 1; j <= k;j++) {   
            if(i>=j) {
                if(j<d) {
                    dp[i][0] = (dp[i][0] + dp[i - j][0]) % mod;
                    dp[i][1] = (dp[i][1] + dp[i - j][1]) % mod;
                }
                else if(j>=d) {
                    dp[i][1] = (dp[i][1] + dp[i - j][0] + dp[i - j][1]) % mod;
                }
            }
        }
    }
    cout << dp[n][1] << endl;
}

```



## Codeforces[DP] LCIS

寒假手速赛第一场

题意:求给定两个序列的公共最长子序列

时间复杂度:$O(n^2)$

思路:增加一个指针，一个记录路径的数组即可

 https://codeforces.com/group/GlRm4CeuZ9/contest/266331/problem/D 

```cpp
#include<bits/stdc++.h>
using namespace std;

const int maxn = 500 + 5;

int a[maxn],s[maxn];
int dp[maxn], pre[maxn];

int main() {
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n;i++){
        scanf("%d", &a[i]);
    }
    int m;
    scanf("%d", &m);
    for (int i = 1; i <= m;i++) {
        scanf("%d", &s[i]);
    }
    //
    for (int i = 1; i <= n;i++) {
        int x = 0;
        for (int j = 1; j <= m; j++) {
            if(a[i]<s[j])
                continue;
            if(a[i]>s[j]) {
                if(dp[j]>dp[x]){
                    x = j;
                }
            }
            if(a[i] == s[j]) {
                if(dp[j]<dp[x]+1) {
                    dp[j] = dp[x] + 1;
                    pre[j] = x;
                }
            }
        }
    }
    //
    int ans = 0;
    int t = 0;

    for (int i = 1; i <= m;i++) {
        if(dp[i]>dp[t]){
            ans = dp[i];
            t = i;
        }
    }
    printf("%d\n", ans);

    vector<int> v;
    while(t) {
        v.push_back(s[t]);
        t = pre[t];
    }

    int sz = v.size();
    for (int i = sz - 1; i >= 0;i--) {
        printf("%d", v[i]);
        if(i == 0) printf("\n");
        else printf(" ");
    }
}

```



## Codeforces[DP] Elevator

 https://codeforces.com/group/GlRm4CeuZ9/contest/266331/problem/E 

题意：某电梯可以承载4个人，上、下电梯均需要1s，上、下一层均需要1s，乘客按照排队顺序上电梯

给定每个乘客所在楼层和目标楼层，问送完所有乘客的最短时间



```cpp
#include<bits/stdc++.h>
using namespace std;
const int maxn = 2000 + 5;
const int inf = 0x3f3f3f3f;
int dp[maxn][10][10][10][10];
int x[maxn], y[maxn];
int n;
int dis(int x,int y) {
    return abs(x - y);
}
int dfs(int i,int cur,int a,int b,int c) {
    if(dp[i][cur][a][b][c])
        return dp[i][cur][a][b][c];
    int ans = inf;
    if(i>n) {
        if(!a&&!b&&!c)
            return 0;
        if(a)
            ans = min(ans, dfs(i, a, 0, b, c) + dis(cur, a) + 1);
        if(b)
            ans = min(ans, dfs(i, b, a, 0, c) + dis(cur, b) + 1);
        if(c)
            ans = min(ans, dfs(i, c, a, b, 0) + dis(cur, c) + 1);
        return dp[i][cur][a][b][c] = ans;
    }
        if(a)
            ans = min(ans, dfs(i, a, 0, b, c) + dis(cur, a) + 1);
        if(b)
            ans = min(ans, dfs(i, b, a, 0, c) + dis(cur, b) + 1);
        if(c)
            ans = min(ans, dfs(i, c, a, b, 0) + dis(cur, c) + 1);
        if(a&&b&&c) {
            ans = min(ans, dfs(i + 1, y[i], a, b, c) + dis(cur, x[i]) + dis(x[i], y[i]) + 2);
            ans = min(ans, dfs(i + 1, a, y[i], b, c) + dis(cur, x[i]) + dis(x[i], a) + 2);
            ans = min(ans, dfs(i + 1, b, a, y[i], c) + dis(cur, x[i]) + dis(x[i], b) + 2);
            ans = min(ans, dfs(i + 1, c, a, b, y[i]) + dis(cur, x[i]) + dis(x[i], c) + 2);
        }
        else {
            if(!a)
                ans = min(ans, dfs(i + 1, x[i], y[i], b, c) + dis(cur, x[i]) + 1);
            else if(!b)
                ans = min(ans, dfs(i + 1, x[i], a, y[i], c) + dis(cur, x[i]) + 1);
            else if(!c)
                ans = min(ans, dfs(i + 1, x[i], a, b, y[i]) + dis(cur, x[i]) + 1);   
        }
        return dp[i][cur][a][b][c] = ans;
}
int main() {
    scanf("%d", &n);
    for (int i = 1;i<=n;i++)
        scanf("%d%d", &x[i], &y[i]);
    printf("%d\n", dfs(1, 1, 0, 0, 0));
}

```







## Codeforces[DP] 状压 Marbles

 https://codeforces.com/contest/1215/problem/E 

```cpp
#include<bits/stdc++.h>
using namespace std;

const int inf = 0x3f3f3f3f;
const int maxn = 4e5 + 100;
int a[maxn];
int tot;

long long pre[30][30], cnt[30];
long long dp[1 << 21];

map<int, int> mp;
int main()
{
    int n;
    scanf("%d", &n);
    for (int i = 1; i <= n;i++) {
        scanf("%d", &a[i]);
        if(!mp[a[i]]){
            mp[a[i]] = tot++;
        }
    }
    for (int i = 1; i <= n;i++) {
        int x = mp[a[i]];
        for (int j = 0; j < tot;j++) {
            pre[x][j] += cnt[j];
        }
        cnt[x]++;
    }
    memset(dp, 0x3f, sizeof(dp));
    dp[0] = 0;
    for (int i = 1; i < (1 << tot); i++) {
        for (int j = 0; j < tot;j++) {
            if(i&(1<<j)) {
                long long temp = 0;
                for (int k = 0; k < tot;k++) {
                    if(j!=k&&!(i&(1<<k))) {
                        temp += pre[j][k];
                    }
                }
                dp[i] = min(dp[i], dp[i ^ (1 << j)] + temp);
            }
        }
    }
    printf("%lld\n", dp[(1 << tot) - 1]);
}

```