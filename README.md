# Template
My Competitive Programming Template

```cpp
v.erase(unique(v.begin(),v.end()),v.end());
```

### 快速幂
::::success[[P1226 【模板】快速幂](https://www.luogu.com.cn/problem/P1226)]
```cpp
int main(){
    A = a , B = b;
    ans = 1%p; // 若p = 1那么ans = 0
    for(;b;b>>=1){
        if(b&1) ans = ans * a % p;
        a = a * a % p;
    }
    printf("%lld^%lld mod %lld=%lld\n",A,B,p,ans);
}
```
::::
### 二分答案  
::::info[提示]
如果能够用 $x$ 次攻击消灭所有的怪物，答案就一定不超过 $x$。如果不能用 $x$ 次攻击消灭所有的怪物，答案就一定比 $x$ 更大。答案具有单调性，可以用二分搜索解决问题。只要能知道怎么判定 $x$ 次攻击是否足够消灭所有的怪物，就可以解决这道题。
::::
::::success[[[ABC063D] Widespread](https://www.luogu.com.cn/problem/AT_arc075_b)]
```cpp lines=16-25
bool check(int x){
    int cnt = 0;
    for(int i = 1;i<=n;i++){
        tmp[i]=h[i] - b * x;
        if(tmp[i]<=0) continue;
        cnt+=tmp[i]/(a-b) + ((tmp[i]%(a-b)) ? 1 : 0);
        if(cnt>x)return false;
    }
    return cnt<=x;
}
signed main(){
    for(int i = 1;i<=n;i++){
        maxx = max(maxx,h[i]);
    }
    sort(h+1,h+n+1,greater<int>());
    int l = 1,r = ceil(1.0*maxx/a)*n,m;
    while(l<=r){
        m = (l+r)>>1;
        if(check(m)){
            ans = m;
            r = m - 1;
        }else{
            l = m + 1;
        }
    }
    cout << ans;
}
```
::::
### 离散化  
核心思想：将超大范围的数据映射到 $1\sim m$ 的整数范围内（排名），保持原始数据的大小关系。  
::::success[Code]
```cpp
/* n表示点数，m表示去重后映射到1~m */
int n,m;
int s[maxn],A[maxn];//A[i]表示排名为i的原始数据
std::unordered_map<int, int> B;//B[x]表示数字x的排名
void solve(){
    for(int i = 1;i<=n;i++){
        A[i] = s[i];
    }
    std::sort(A+1,A+n+1);
    m = std::unique(A+1,A+n+1) - (A+1);
    for(int i = 1;i<=m;i++){
        B[A[i]] = i;
    }
    for(int i = 1;i<=n;i++){
        s[i] = B[s[i]];  //s[i]映射到1~m
    }
}
```
::::
### 三分/函数
找单峰函数的极值。
::::success[[P1883 【模板】三分](https://www.luogu.com.cn/problem/P1883)  ]
```cpp
double solve(){
    double l = 0,r = 1000,mid1,mid2;
    while(r-l>eps){
        mid1 = l + (r-l)/3;
        mid2 = l + (r-l)/3 * 2;
        if(f(mid1) > f(mid2)){
            //[mid1,r]
            l = mid1;
        }else{
            //[l,mid2]
            r = mid2;
        }
    }
    return f(l);
}
```
::::
### 单调队列/滑动窗口
长度为 $k$ 的窗口，求窗口内最大值。
::::success[[P1886 【模板】单调队列](https://www.luogu.com.cn/problem/P1886)  ]
```cpp
deque<int>Q;//Q里存放下标
int main(){
    for(int i = 1;i<=n;i++){
        //约束1：里面要是[i-k+1~i]，front必须>=i-k+1
        while(!Q.empty() && Q.front()<i-k+1) Q.pop_front();
        //约束2：front必须是唯一的那个最大数字的下标，那么Q要单调递减
        while(!Q.empty() && a[i] >= a[Q.back()]) Q.pop_back();
        Q.push_back(i);
        if(i>=k)cout << a[Q.front()] << endl;
    }
}
```
::::
### 单调栈
- 寻找以某个值为最小/大值的最大区间（正反各跑一边）。
- 给出项数为 $n$ 的整数数列 $a_{1 \dots n}$。
定义函数 $f(i)$ 代表数列中第 $i$ 个元素之后第一个大于 $a_i$ 的元素的**下标**，即 $f(i)=\min_{i<j\leq n, a_j > a_i} \{j\}$。若不存在，则 $f(i)=0$。

::::success[[P5788 【模板】单调栈](https://www.luogu.com.cn/problem/P5788)  ]
```cpp
int main(){
    cin >> n;
    for(int i = 1;i<=n;i++){
        cin >> a[i];
        /* 维护一个单调递减的栈 */
        if(!S.empty() && a[i] > a[S.top()]){ //大了：说明这个是第一个比栈顶大的元素
            while(!S.empty() && a[i] > a[S.top()]){ //大的全记录了
                f[S.top()] = i;
                S.pop();
            }
        }
        S.push(i);
    }
}
```
::::
### 线段树
1. 将某区间每一个数加上 $k$。
2. 求出某区间每一个数的和。
::::success[[P3372 【模板】线段树 1](https://www.luogu.com.cn/problem/P3372)]
```cpp
#define int long long
class segmentTree{
private:
    #define lc p<<1
    #define rc p<<1|1
    struct node{
        int l,r,sum,lazy;
    }t[maxn<<2];
public:
    void pushup(int p){
        t[p].sum = t[lc].sum + t[rc].sum;
    }
    void build(int p,int l,int r){
        t[p]={l,r,a[l],0};
        if(l==r)return;
        int m=(l+r)>>1;
        build(lc,l,m);
        build(rc,m+1,r);
        pushup(p);
    }
    void pushdown(int p){
        /* 有lazy的节点自己是改好的，下传 */
        if(t[p].lazy){
            t[lc].sum+=t[p].lazy*(t[lc].r-t[lc].l+1);
            t[rc].sum+=t[p].lazy*(t[rc].r-t[rc].l+1);
            t[lc].lazy+= t[p].lazy;
            t[rc].lazy+= t[p].lazy;
            t[p].lazy = 0;
        }
    }
    void update(int p,int x,int y,int k){
        if(x<=t[p].l && y>=t[p].r){
            t[p].sum+=(t[p].r-t[p].l+1)*k;
            t[p].lazy+=k;
            return;
        }
        int m = (t[p].l + t[p].r) >> 1;
        pushdown(p);
        if(x<=m) update(lc,x,y,k);
        if(y>m) update(rc,x,y,k);
        pushup(p);
    }
    int query(int p,int x,int y){
        if(x<=t[p].l && y>=t[p].r){
            return t[p].sum;
        }
        int ret = 0;
        int m = t[p].l + t[p].r >> 1;
        pushdown(p);
        if(x<=m) ret += query(lc,x,y);
        if(y>m) ret += query(rc,x,y);
        return ret;
    }
}T;
```
::::
### 并查集
::::success[[P3367 【模板】并查集](https://www.luogu.com.cn/problem/P3367)]
```cpp
int Find(int x){
    if(fa[x]==x)return x;
    else return fa[x] = Find(fa[x]);
}
void Merge(int A,int B){
    A = Find(A); B = Find(B);
    if(A!=B) fa[A] = B;
}
```
::::
### 最小生成树
有 $n$ 朵云，你要将它们连成 $k$ 个棉花糖，将 $X_i$ 云朵和 $Y_i$ 连接起来需要 $L_i$ 的代价，求最小代价。
::::success[[P1195 口袋的天空](https://www.luogu.com.cn/problem/P1195)]
```cpp
int fa[1010];  // 定义父亲
struct edge {
  int u, v, w;
}g[10010];
int l;
void add(int u, int v, int w) {
  g[++l].u = u; g[l].v = v; g[l].w = w;
}
// 标准并查集
int findroot(int x) { return fa[x] == x ? x : fa[x] = findroot(fa[x]); }
void Merge(int x, int y) {
  x = findroot(x);
  y = findroot(y);
  fa[x] = y;
}
bool cmp(edge A, edge B) { return A.w < B.w; }
// Kruskal 算法
void kruskal() {
  int tot = 0;  // 存已选了的边数
  int ans = 0;  // 存总的代价
  for (int i = 1; i <= m; i++) {
    int xr = findroot(g[i].u), yr = findroot(g[i].v);
    if (xr != yr) {        // 如果父亲不一样
      Merge(xr, yr);       // 合并
      tot++;               // 边数增加
      ans += g[i].w;       // 代价增加
      if (tot == n - k) {  // 检查选的边数是否满足 k 个棉花糖
        cout << ans << '\n';
        return;
      }
    }
  }
  cout << "No Answer\n";  // 无法连成
}
int main() {
  for (int i = 1; i <= n; i++) {  // 初始化
    fa[i] = i;
  }
  for (int i = 1; i <= m; i++) {
    add(u, v, w);  // 添加边
  }
  sort(g + 1, g + m + 1, cmp);  // 先按边权排序
  kruskal();
}
```
::::
### 最近公共祖先 LCA | 链式前向星
::::success[[P3379 【模板】最近公共祖先（LCA）](https://www.luogu.com.cn/problem/P3379)]
```cpp
int to[maxn],nxt[maxn];
int tot,Head[maxn],depth[maxn];
int up[maxn][25];
/* 链式前向星写法 */
void AddEdge(int u,int v){
	++tot;
	to[tot] = v;
	nxt[tot] = Head[u];
	Head[u] = tot;
}
void dfs(int u,int fa){
	depth[u] = depth[fa] + 1;
	up[u][0] = fa;
	for(int i = 1;i<=19;i++){
		up[u][i] = up[up[u][i-1]][i-1];
	}
	for(int i = Head[u];i;i = nxt[i]){
		if(to[i] == fa)continue;
		else dfs(to[i],u);
	}
}
int LCA(int a,int b){
	if(depth[a] < depth[b]) swap(a,b);
	// A应该更深
	// 移动到相同的深度
	for(int i = 19;i>=0;i--){
		if(depth[up[a][i]] < depth[b])continue;
		a = up[a][i];
	}
	if(a == b) return b;
	// 向上跳同样的距离
	for(int i = 19;i>=0;i--){
		if(up[a][i] != up[b][i]){
			a = up[a][i];
			b = up[b][i];
		}
	}
	return up[a][0];
}
int main(){
	for(int i = 1;i<n;i++){
		AddEdge(u,v);
		AddEdge(v,u);
	}
	dfs(s,0);
	for(int i = 1;i<=m;i++){
		printf("%d\n",LCA(a,b));
	}
}
```
::::
### 单源最短路径
::::success[[P4779 【模板】单源最短路径（标准版）](https://www.luogu.com.cn/problem/P4779)]
```cpp
const int maxn = 2e5+5, inf = (1<<30);
class Graph{
public:
    int dis[maxn]; bool vis[maxn];
    struct node{
        int to,dis;
        friend bool operator >(node A, node B){
            return A.dis > B.dis;
        }
    };
    int tot,to[maxn<<1],nxt[maxn<<1],head[maxn<<1],w[maxn];
priority_queue<node,vector<node>,greater<node> > Q;
    void addEdge(int u,int v,int W){
        to[++tot] = v;
        nxt[tot] = head[u];
        head[u] = tot;
        w[tot] = W;
    }
    void init(){
        for(int i = 1;i<=n;i++){
            dis[i] = inf;
        }
        dis[s] = 0;
    }
    void dij(){
        Q.push({s,0});
        while(!Q.empty()){
            node N = Q.top(); Q.pop();
            if(vis[N.to]) continue;
            vis[N.to] = true;
            for(int i = head[N.to];i;i = nxt[i]){
                if(dis[to[i]] > dis[N.to] + w[i]){
                    dis[to[i]] = dis[N.to] + w[i];
                    Q.push((node){to[i],dis[to[i]]});
                }
            }
        }
    }
}G;
int main(){
    for(int i = 1;i<=m;i++){
        G.addEdge(u,v,w);
    }
    G.init();
    G.dij();
}
```
::::
### 连通性相关
#### 强连通分量
强连通的定义是：有向图 G 强连通是指，G 中任意两个结点连通。
::::success[Tarjan 算法求强连通分量]
```cpp
int dfn[N], low[N], dfncnt, s[N], in_stack[N], tp;
int scc[N], sc;  // 结点 i 所在 SCC 的编号
int sz[N];       // 强连通 i 的大小

void tarjan(int u) {
  low[u] = dfn[u] = ++dfncnt, s[++tp] = u, in_stack[u] = 1;
  for (int i = h[u]; i; i = e[i].nex) {
    const int &v = e[i].t;
    if (!dfn[v]) {
      tarjan(v);
      low[u] = min(low[u], low[v]);
    } else if (in_stack[v]) {
      low[u] = min(low[u], dfn[v]);
    }
  }
  if (dfn[u] == low[u]) {
    ++sc;
    do {
      scc[s[tp]] = sc;
      sz[sc]++;
      in_stack[s[tp]] = 0;
    } while (s[tp--] != u);
  }
}
```
::::
#### 双连通分量
在一张连通的无向图中，对于两个点 $u$ 和 $v$，如果无论删去哪条边（只能删去一条）都不能使它们不连通，我们就说 $u$ 和 $v$ 边双连通。
::::success[Tarjan 算法求双连通分量]
```cpp
int n, m, ans;
int tot = 1, hd[N];
struct edge {
  int to, nt;
} e[M << 1];
void add(int u, int v) { e[++tot].to = v, e[tot].nt = hd[u], hd[u] = tot; }
void uadd(int u, int v) { add(u, v), add(v, u); }
bool bz[M << 1];
int bcc_cnt, dfn[N], low[N], vis_bcc[N];
vector<vector<int>> bcc;
void tarjan(int x, int in) {
  dfn[x] = low[x] = ++bcc_cnt;
  for (int i = hd[x]; i; i = e[i].nt) {
    int v = e[i].to;
    if (dfn[v] == 0) {
      tarjan(v, i);
      if (dfn[x] < low[v]) bz[i] = bz[i ^ 1] = true;
      low[x] = min(low[x], low[v]);
    } else if (i != (in ^ 1))
      low[x] = min(low[x], dfn[v]);
  }
}
void dfs(int x, int id) {
  vis_bcc[x] = id, bcc[id - 1].push_back(x);
  for (int i = hd[x]; i; i = e[i].nt) {
    int v = e[i].to;
    if (vis_bcc[v] || bz[i]) continue;
    dfs(v, id);
  }
}
int main() {
  for (int i = 1; i <= m; i++) {
    if (u == v) continue;
    uadd(u, v);
  }
  for (int i = 1; i <= n; i++)
    if (dfn[i] == 0) tarjan(i, 0);
  for (int i = 1; i <= n; i++)
    if (vis_bcc[i] == 0) {
      bcc.push_back(vector<int>());
      dfs(i, ++ans);
    }
  cout << ans << '\n';
  for (int i = 0; i < ans; i++) {
    cout << bcc[i].size();
    for (int j = 0; j < bcc[i].size(); j++) cout << ' ' << bcc[i][j];
    cout << '\n';
  }
}
```
::::
#### 割点
对于一个无向图，如果把一个点删除后这个图的极大连通分量数增加了，那么这个点就是这个图的割点（又称割顶）。
::::success[Tarjan 算法求割点]
```cpp
//洛谷 P3388 【模板】割点（割顶）
int dfn[100001], low[100001], idx, res;
// dfn：记录每个点的时间戳
// low：能不经过父亲到达最小的编号，idx：时间戳，res：答案数量
bool vis[100001], flag[100001];  // flag: 答案 vis：标记是否重复
vector<int> edge[100001];        // 存图用的

void Tarjan(int u, int fa) {  // u 当前点的编号，fa 自己爸爸的编号
  vis[u] = true;              // 标记
  low[u] = dfn[u] = ++idx;    // 打上时间戳
  int child = 0;              // 每一个点儿子数量
  for (const auto &v : edge[u]) {  // 访问这个点的所有邻居 （C++11）
    if (!vis[v]) {
      child++;                       // 多了一个儿子
      Tarjan(v, u);                  // 继续
      low[u] = min(low[u], low[v]);  // 更新能到的最小节点编号
      if (fa != u && low[v] >= dfn[u] && !flag[u]) { 
        // 如果不是自己，且不通过父亲返回的最小点符合割点的要求，并且没有被标记过
        // 要求即为：删了父亲连不上去了，即为最多连到父亲
        flag[u] = true;
        res++;  // 记录答案
      }
    } else if (v != fa) {
      // 如果这个点不是自己的父亲，更新能到的最小节点编号
      low[u] = min(low[u], dfn[v]);
    }
  }
  // 主要代码，自己的话需要 2 个儿子才可以
  if (fa == u && child >= 2 && !flag[u]) {
    flag[u] = true;
    res++;  // 记录答案
  }
}

int main() {
  for (int i = 1; i <= m; i++) {
    edge[x].push_back(y);
    edge[y].push_back(x);
  }
  for (int i = 1; i <= n; i++)  //  Tarjan 图不一定连通
    if (!vis[i]) {
      idx = 0;       // 时间戳初始为 0
      Tarjan(i, i);  // 从第 i 个点开始，父亲为自己
    }
  for (int i = 1; i <= n; i++)
    if (flag[i]) cout << i << " ";
}
```
::::
#### 割边
对于一个无向图，如果删掉一条边后图中的连通分量数增加了，则称这条边为桥或者割边。严谨来说，就是：假设有连通图 $G=\{V,E\}$，$e$ 是其中一条边（即$e \in E$），如果 $G-e$ 是不连通的，则 $e$ 是图 $G$ 的一条割边（桥）。
::::success[Tarjan 算法求桥]
```cpp
/* 其中，当 isbridge[x] 为真时，(father[x],x) 为一条割边。 */
int low[MAXN], dfn[MAXN], idx;
bool isbridge[MAXN];
vector<int> G[MAXN];
int cnt_bridge;
int father[MAXN];
void tarjan(int u, int fa) {
  father[u] = fa;
  low[u] = dfn[u] = ++idx;
  for (const auto &v : G[u]) {
    if (!dfn[v]) {
      tarjan(v, u);
      low[u] = min(low[u], low[v]);
      if (low[v] > dfn[u]) {
        isbridge[v] = true;
        ++cnt_bridge;
      }
    } else if (v != fa) {
      low[u] = min(low[u], dfn[v]);
    }
  }
}
```
::::
### 字典树
给定 $n$ 个模式串 $s_1, s_2, \dots, s_n$ 和 $q$ 次询问，每次询问给定一个文本串 $t_i$，请回答 $s_1 \sim s_n$ 中有多少个字符串 $s_j$ 满足 $t_i$ 是 $s_j$ 的**前缀**。
输入的字符串大小敏感。例如，字符串 `Fusu` 和字符串 `fusu` 不同。
::::success[[P8306 【模板】字典树](https://www.luogu.com.cn/problem/P8306)]
```cpp
int T,n,q; string s;
class Trie{
private:
    int tr[maxn][70],tot,cnt[maxn];
    bool end[maxn];
    int hash(char c){
        if(c>='a' && c<='z'){
            return c-'a';
        }
        if(c>='A' && c<='Z'){
            return c-'A'+26;
        }
        if(c>='0' && c<='9'){
            return c-'0'+52;
        }
    }
public:
    void clear(){
        for(int i = 0;i<tot+1;i++){
            for(int j = 0;j<70;j++){
                tr[i][j]=0;
            }
            cnt[i] = end[i] = 0;
        }
        tot=1;
    }
    void insert(string x){
        int len = x.size(); int p = 1;//p:根节点
        cnt[p]++;
        for(int i = 0;i<len;i++){
            if(!tr[p][hash(x[i])]){
                tr[p][hash(x[i])]=++tot;//连边
            }
            p = tr[p][hash(x[i])];
            cnt[p]++;
        }
        end[p] = true;
    }
    int query(string x){
        int p=1;
        int len = x.size();
        for(int i = 0;i<len;i++){
            if(!tr[p][hash(x[i])]){
                return 0;
            }else{
                p = tr[p][hash(x[i])];
            }
        }
        return cnt[p];
    }
}t;
int main(){
    cin >> T;
    while(T--){
        t.clear();
        for(int i = 1;i<=n;i++){
            t.insert(s);
        }
    }
}
```
::::
### 二元一次不定方程 | gcd
$\gcd$ ：最大公约数  
$ex\gcd$：求 $ax+by=\gcd(a,b)$ 的整数解
::::success[gcd和exgcd]
```cpp
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }

ll x,y; 
void exgcd(ll a,ll b){
	if(b==0){
		x=1; y=0;
		return;
	}
	exgcd(b,a%b);
	ll t=x;
	x=y;
	y=t-a/b*y;
}
```
::::
给定不定方程

$$ax+by=c$$

若该方程无整数解，输出 $-1$。  
若该方程有整数解，且有正整数解，则输出其**正整数**解的数量，所有**正整数**解中 $x$ 的最小值，所有**正整数**解中 $y$ 的最小值，所有**正整数**解中 $x$ 的最大值，以及所有**正整数**解中 $y$ 的最大值。  
若方程有整数解，但没有正整数解，你需要输出所有**整数解**中 $x$ 的最小正整数值， $y$ 的最小正整数值。
::::success[[P5656 【模板】二元一次不定方程 (exgcd)](https://www.luogu.com.cn/problem/P5656)]
```cpp
ll x,y;
ll exgcd(ll a,ll b){
	if(b==0){
		x=1; y=0;
		return a;
	}
	ll d=exgcd(b,a%b);
	ll t=x;
	x=y;
	y=t-a/b*y;
	return d;//顺便把最大公因数也给求出来了
}
int main(){
	while(t--){
		ll d=exgcd(a,b);//求出d
		if(c%d){//不满足裴蜀定理，直接输出-1
			printf("-1\n");
			continue;
		}
		x=x*c/d,y=y*c/d;//求出x0和y0，tx和ty
		ll tx=b/d,ty=a/d;
		ll k=ceil((1.0-x)/tx);//求出k
		x+=tx*k;
		y-=ty*k;	
		if(y<=0){//如果没有正整数解，算出ymin之后输出
			ll ymin=y+ty*1ll*ceil((1.0-y)/ty);
			printf("%lld %lld\n",x,ymin);
		}
		else{
			printf("%lld ",(y-1)/ty+1);
			printf("%lld ",x);
			printf("%lld ",(y-1)%ty+1);
			printf("%lld ",x+(y-1)/ty*tx);
			printf("%lld\n",y);
		}
	}
}
```
::::
### 二维偏序 | 离线二维数点 | BIT
给你一个长为 $n$ 的序列 $a$，有 $m$ 次询问，每次询问给定 $l,r,x$，求 $[l,r]$ 区间中小于等于 $x$ 的元素个数。
::::success[[P10814 【模板】离线二维数点](https://www.luogu.com.cn/problem/P10814)]
```cpp
struct node {
	int k, id, x, val;
	bool operator < (const node &a) const {
		return k < a.k;
	}
} q[N << 1];
int a[N], cnt, ans[N];
struct BinaryTree {
	int t[N];
	void add(int x) { //把t[x]++改成t[x]+=k实现单点增加任意值
		while (x < N) {
			t[x]++;
			x += lowbit(x);
		}
	}
	int query(int x) { //求1~x前缀和
		int res = 0;
		while (x) {
			res += t[x];
			x -= lowbit(x);
		}
		return res;
	}
} bit;
signed main() {
	for (int i = 1; i <= m; ++i) {
		int l, r, x; read(l, r, x);
		q[++cnt] = {l-1, i, x, -1};
		q[++cnt] = {r, i, x, 1};
	}
	sort(q + 1, q + 1 + cnt);
	for (int i = 1, j = 1; i <= cnt; ++i) {
		for (; j <= q[i].k; ++j) bit.add(a[j]);
		ans[q[i].id] += q[i].val * bit.query(q[i].x);
	}
	for (int i = 1; i <= m; ++i) write(ans[i]);
}
```
::::
