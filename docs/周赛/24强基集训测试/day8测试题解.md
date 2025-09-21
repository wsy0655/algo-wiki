## T1


奖励来自于移除的边数的权值总和，为了使得移动的边数总权值之和最大，应该使得留下的边的权值之和最小。

因此答案可以粗略地认为是总边权之和减去最小生成树的边权之和。

注意到负数边移除显然不优，因此在 Kruskal 合并的过程中：

- 若 `u v` 已处于同一集合，但当前边权 $w$ 为负数，也应当 **不移除**。
- 即虽然选择进来形成环，但若选择移除会降低收益，因此也应当考虑进来。


```cpp
struct edge
{
    int u, v, w;
};
vector<edge> e;
bool cmp(edge x, edge y)
{
    return x.w < y.w;
}
long long kruskal()
{
    sort(e.begin(), e.end(), cmp); // 按照边权从小到大排序
    for (int i = 1; i <= n; i++) fa[i] = i; // 初始化并查集
    long long ans = 0;
    for (auto it : e)
    {
        int u = it.u, v = it.v, w = it.w;
        if (find(u) != find(v))
        {
            ans += w;
            merge(u, v);
        }
        else if (w < 0) // 负数边也得要
        {
            ans += w;
        }
    }
    return ans;
}
int main()
{
    long long sum = 0;
    while (m--)
    {
        int u, v, w;
        cin >> u >> v >> w;
        e.push_back({u, v, w});
        sum += w;
    }
    sum -= kruskal();
    cout << sum;
    return 0;
}
```


___





## T2

比较模板的拆点分层图最短路，下面分别给出两种实现：


**拆点**

定义 $dis_{i,j}$ 表示走到 $i$ 且还剩余 $j$ 次卡片的最短路。

答案：$\min(dis_{n,0},dis_{n,1},\ldots,dis_{n,k})$

转移：

设取出的队头状态为 `(d, u, j)`，其中 $d$ 为 $dis_{u,j}$，$u$ 为点的编号，$j$ 为剩余卡片次数。

枚举 $u$ 的邻点 $v$：

- 不用卡片，则 $dis_{v,j}=\min(dis_{v,j},dis_{u,j}+w)$
- 若用卡片，保证 $j>0$，则 $dis_{v,j-1}=\min(dis_{v,j-1},dis_{u,j}+w / 2)$


```cpp
void dijkstra(int s)
{
    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= k; j++)
            dis[i][j] = 1e9;
    dis[s][k] = 0;
    priority_queue<ary, vector<ary>, greater<ary>> q; // ary = array<int, 3>
    q.push({0, s, k});
    while (!q.empty())
    {
        auto now = q.top();
        q.pop();
        int d = now[0], u = now[1], j = now[2];
        if (vis[u][j]) continue;
        vis[u][j] = 1;
        for (auto it : e[u])
        {
            int v = it.first, w = it.second;
            if (dis[v][j] > dis[u][j] + w)
            {
                dis[v][j] = dis[u][j] + w;
                q.push({dis[v][j], v, j});
            }
            if (j > 0 && dis[v][j - 1] > dis[u][j] + w / 2)
            {
                dis[v][j - 1] = dis[u][j] + w / 2;
                q.push({dis[v][j - 1], v, j - 1});
            }
        }
    }
}
```


**分层图实现**


分层图实现就是拆点的具体体现，把每个点拆分出的 $k+1$ 个点都建立出来。一共 $k+1$ 层图。

注意建立分层图以后，`dijkstra` 里就是模板代码了，关键在于建图。

- 以及关键的数组大小要开到 $50\times (50 + 1) + 5$，即最多要用到 $n\times (k+1)$ 个点。


```cpp
constexpr int N = 55 * 55 + 5
int n, m, k, dis[N], vis[N]
vector<pair<int, int>> e[N]
void dijkstra(int s)
{
    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= k; j++)
            dis[i + j * n] = 1e9
    dis[s] = 0;
    priority_queue<pii，vector<pii>, greater<pii>> q // pii = pair<int, int>
    q.push({0, s})
    while (!q.empty())
    {
        auto now = q.top()
        q.pop()
        int d = now.first, u = now.second
        if (vis[u]) continue
        vis[u] = 1
        for (auto it : e[u])
        {
            int v = it.first, w = it.second
            if (dis[v] > dis[u] + w)
            {
                dis[v] = dis[u] + w
                q.push({dis[v], v})
            }
        }
    }
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0)
    cin >> n >> m >> k
    while (m--)
    {
        int u, v, w
        cin >> u >> v >> w
        // 层内正常连
        for (int j = 0; j <= k; j++)
        {
            e[u + j * n].push_back({v + j * n, w})
            e[v + j * n].push_back({u + j * n, w})
        }
        // 层与层之间
        for (int j = 1; j <= k; j++)
        {
            e[u + (j - 1) * n].push_back({v + j * n, w / 2})
            e[v + (j - 1) * n].push_back({u + j * n, w / 2})
        }
    }
    dijkstra(1)
    int ans = 1e9
    for (int i = 0; i <= k; i++) ans = min(ans, dis[n + i * n])
    cout << ans
    return 0
}
```


___


## T3


一个朴素的做法是：

定义 `vector<int> bel[200005]` 预处理元素 $1\sim m$ 都出现在过哪些集合。


```cpp
for (int i = 1; i <= n; i++) // 集合编号 1 ~ n
{
    int a;
    cin >> a;
    while (a--)
    {
        int x;
        cin >> x;
        bel[x].push_back(i);
    }
}
```

考虑图论建模，对有公共元素的集合互相长度为 $1$ 的无向边，则从 $1$ 个集合走到另一个集合就类似于集合的合并过程。

那么我们要做的就是对每个元素 $i$，其中 $i\in [1,m]$，枚举含有 $i$ 的集合编号 $j$。

- `for (auto j : bel[i])`。

把这些集合 $j$ 两两暴力连边即可。

```cpp
for (int i = 1; i <= m; i++)
{
    for (int j = 0; j < bel[i].size(); j++)
    {
        for (int k = j + 1; k < bel[i].size(); k++)
        {
            // 拥有元素 i 的集合互相连边
            e[bel[i][j]].push_back(bel[i][k]);
            e[bel[i][k]].push_back(bel[i][j]);
        }
    }
}
```

然后我们从所有含有元素 $1$ 的集合开始，进行 BFS，找到一个含有元素 $m$ 的集合的最短路。

这其实就是一个 **多源BFS** 问题，可以一次性将所有起点都入队处理。（建立一个超级源点 $0$，向起点连接长度为 $0$ 的边也不是不可以）


```cpp
queue<int> q;
vector<bool> vis(n + 1, false);
vector<int> dis(n + 1, 1e9);
for (auto j : bel[1]) 
{
    q.push(j); // 拥有元素 1 的集合都入队
    vis[j] = 1;
    dis[j] = 0;
}
```


接下来执行 BFS 即可，最后枚举所有含有元素 $m$ 的集合找到答案。

```cpp
while (!q.empty())
{
    int u = q.front();
    q.pop();
    for (auto v : e[u])
    {
        if (!vis[v])
        {
            dis[v] = dis[u] + 1;
            q.push(v);
            vis[v] = 1;
        }
    }
}
int ans = 1e9;
for (auto j : bel[m]) ans = min(ans, dis[j]); // 拥有元素 m 的所有集合取 min
if (ans == 1e9) cout << -1;
else cout << ans;
```


这样做时间复杂度较高，原因在于建图炸了。


容易想到对每一个元素所在的所有集合建立一个虚拟节点。


然后所有含有元素 $i$ 的集合 $j$ 向虚拟节点连接一条长度为 $1$ 的有向边，同时虚拟节点向 $j$ 连接一条长度为 $0$ 的有向边。

这样就优化建图，实现中转功能了。


注意建立虚拟节点以后，给题目带来的空间复杂度提升的问题。

参考代码采用 动态定义 `vector` 实现，请自己区别。

同时注意：边权有 $0$ 或 $1$，无法使用普通的 BFS，你可以写 0-1 BFS 或 `dijkstra` 都行。本代码采用 0-1 BFS。


```cpp
#include <bits/stdc++.h>
using namespace std;
using pii = pair<int, int>;
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n, m;
    cin >> n >> m;
    vector<vector<int>> bel(m + 1); // 预处理每个元素属于哪些集合
    for (int i = 1; i <= n; i++)
    {
        int a;
        cin >> a;
        for (int j = 1; j <= a; j++)
        {
            int x;
            cin >> x;
            bel[x].push_back(i); // x 在集合 i 出现过
        }
    }
    vector<vector<pii>> e(n + m + 1); // 空间要多 m 个给虚拟节点用
    for (int i = 1; i <= m; i++)
    {
        int vi = n + i;
        for (auto j : bel[i])
        {
            e[j].push_back({vi, 1});
            e[vi].push_back({j, 0});
        }
        // for (int j = 0; j < bel[i].size(); j++)
        // {
        //     for (int k = j + 1; k < bel[i].size(); k++)
        //     {
        //         // 拥有元素 i 的集合互相连边
        //         e[bel[i][j]].push_back(bel[i][k]);
        //         e[bel[i][k]].push_back(bel[i][j]);
        //     }
        // }
    }
    deque<int> q;
    vector<bool> vis(n + m + 1, false);  // 空间要多 m 个给虚拟节点用
    vector<int> dis(n + m + 1, 1e9);  // 空间要多 m 个给虚拟节点用
    for (auto j : bel[1]) 
    {
        q.push_back(j); // 拥有元素 1 的集合都入队
        vis[j] = 1;
        dis[j] = 0;
    }
    
    while (!q.empty())
    {
        int u = q.front();
        q.pop_front();
        for (auto it : e[u])
        {
            int v = it.first, w = it.second;
            if (!vis[v])
            {
                vis[v] = 1;
                dis[v] = dis[u] + w;
                if (w) q.push_back(v);
                else q.push_front(v);
            }
        }
    }
    int ans = 1e9;
    for (auto j : bel[m]) ans = min(ans, dis[j]); // 拥有元素 m 的所有集合取 min
    if (ans == 1e9) cout << -1;
    else cout << ans;
    return 0;
}
```