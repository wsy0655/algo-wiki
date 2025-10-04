## [ABC416E] Development

<details>

<summary>代码</summary>


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 5e2 + 5;
int n, m, k, t, q, f[N][N];
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 0; i <= n; i++)
        for (int j = 0; j <= n; j++)
            if (i != j)
                f[i][j] = 1e18;
    while (m--)
    {
        int u, v, w;
        cin >> u >> v >> w;
        f[u][v] = f[v][u] = min(f[u][v], w);
    }
    cin >> k >> t;
    for (int i = 1; i <= k; i++)
    {
        int x;
        cin >> x;
        f[x][0] = t;
        f[0][x] = 0;
    }
    for (int k = 0; k <= n; k++)
        for (int i = 0; i <= n; i++)
            for (int j = 0; j <= n; j++)
                f[i][j] = min(f[i][j], f[i][k] + f[k][j]);
    cin >> q;
    while (q--)
    {
        int op;
        cin >> op;
        if (op == 1)
        {
            int u, v, w;
            cin >> u >> v >> w;
            f[u][v] = f[v][u] = min(f[u][v], w);
            for (int i = 0; i <= n; i++)
            {
                for (int j = 0; j <= n; j++)
                {
                    f[i][j] = min(f[i][j], f[i][u] + w + f[v][j]);
                    f[i][j] = min(f[i][j], f[i][v] + w + f[u][j]);
                }
            }
        }
        if (op == 2)
        {
            int x;
            cin >> x;
            f[x][0] = t;
            f[0][x] = 0;
            for (int i = 0; i <= n; i++)
            {
                for (int j = 0; j <= n; j++)
                {
                    f[i][j] = min(f[i][j], f[i][x] + t + f[0][j]);
                    f[i][j] = min(f[i][j], f[i][0] + 0 + f[x][j]);
                }
            }
        }
        if (op == 3)
        {
            int ans = 0;
            for (int i = 1; i <= n; i++)
                for (int j = 1; j <= n; j++)
                    if (f[i][j] != 1e18)
                        ans += f[i][j];
            cout << ans << "\n";
        }
    }
    return 0;
}
```

</details>


___

## [CSP-S 2022] 假期计划

**暴力 $60$ 分**

$$
\begin{array}{ll}
1 & \textbf{for } a \gets 2 \textbf{ to } n \\
2 & \qquad \textbf{if } f[1][a] > k + 1 \textbf{ then continue} \\
3 & \qquad \textbf{for } b \gets 2 \textbf{ to } n \\
4 & \qquad\qquad \textbf{if } f[a][b] > k + 1 \ \textbf{or } a=b \textbf{ then continue} \\
5 & \qquad\qquad \textbf{for } c \gets 2 \textbf{ to } n \\
6 & \qquad\qquad\qquad \textbf{if } f[b][c] > k + 1 \ \textbf{or } b=c \ \textbf{or } a=c \textbf{ then continue} \\
7 & \qquad\qquad\qquad \textbf{for } d \gets 2 \textbf{ to } n \\
8 & \qquad\qquad\qquad\qquad \textbf{if } f[c][d] > k + 1 \ \textbf{or } f[d][1] > k + 1 \ \textbf{or } d \in \{a,b,c\} \textbf{ then continue} \\
9 & \qquad\qquad\qquad\qquad\qquad ans \gets \max(ans, \ s[a]+s[b]+s[c]+s[d]) \\
\end{array}
$$



<details>

<summary>满分代码</summary>


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int N = 2.5e3 + 5;
int n, m, k, w[N], f[N][N], ans;
vector<int> e[N];
vector<int> g[N]; // 预处理每个点可以到哪些点
void bfs(int s, int dis[])
{
    queue<int> q;
    q.push(s);
    dis[s] = 0; 
    while (q.size())
    {
        int u = q.front();
        q.pop();
        for (auto v : e[u])
        {
            if (dis[v] == 1e9)
            {
                dis[v] = dis[u] + 1;
                // 保留可达的点，1 号点不要，其次要保证 1 可以在 k 步内到达，且 s 到达 v 也可以在 k 步以内。
                if (v != 1 && f[1][v] <= k && dis[v] <= k)
                {
                    g[s].push_back(v); // s 可以到 v
                }
                if (g[s].size() > 3) // 个数多于 3，删除权值较小的点
                {
                    sort(g[s].begin(), g[s].end(), [&](int x, int y) {
                        return w[x] > w[y];
                    });
                    g[s].pop_back(); // 删除权值小的
                }    
                q.push(v);
            }
        }
    }
}
int main()
{
    cin >> n >> m >> k;
    k++; // 直接令 k + 1 即可
    for (int i = 2; i <= n; i++) cin >> w[i];
    while (m--)
    {
        int x, y;
        cin >> x >> y;
        e[x].push_back(y);
        e[y].push_back(x);
    }
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= n; j++)
        {
            f[i][j] = 1e9; // 初始化最短路不存在
        }
        bfs(i, f[i]);
    }
    for (int b = 2; b <= n; b++)
    {
        for (int c = 2; c <= n; c++)
        {
            if (b == c || f[b][c] > k) continue;
            for (auto a : g[b])
            {
                for (auto d : g[c])
                {
                    if (a == d || a == c || d == b) continue;
                    if (f[1][a] > k || f[a][b] > k || f[c][d] > k || f[d][1] > k) continue;
                    ans = max(ans, w[a] + w[b] + w[c] + w[d]); 
                }
            }
        }
    }
    cout << ans;
    return 0;
}
```

</details>


____


## [TJOI2019] 大中锋的游乐场


<details>

<summary>满分代码</summary>


```cpp
#include <bits/stdc++.h>
using namespace std;
using ary = array<int, 2>;

void solve()
{
    int n, m, k;
    cin >> n >> m >> k;
    vector<int> a(n + 1);
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        // 这里为了方便将汉堡设为 -1
        if (a[i] == 2)
            a[i] = -1;
    }
    vector<vector<ary>> e(n + 1);
    while (m--)
    {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].push_back({v, w});
        e[v].push_back({u, w});
    }
    int s, t;
    cin >> s >> t;
    vector<vector<int>> dis(n + 1, vector<int>(25, 1e9)), vis(n + 1, vector<int>(25, 0));
    // dis[i][j] : 走到点 i 且可乐减去汉堡差值为 j 的最短路
    // 堆里存储三个信息，[距离，差值，编号]
    priority_queue<array<int, 3>, vector<array<int, 3>>, greater<array<int, 3>>> q;
    // 初始化起点
    dis[s][a[s] + 10] = 0;
    q.push({0, a[s] + 10, s});
    while (q.size())
    {
        auto [d, i, u] = q.top();
        q.pop();
        // Dijkstra：已经加入 S 集合代表最短路已确定就跳过
        if (vis[u][i])
            continue;
        vis[u][i] = 1;
        // auto [v, w] 结构化绑定 C++ 17语法，信奥赛目前禁用
        for (auto [v, w] : e[u])
        {
            if (a[v] == 1 && abs(i + 1 - 10) <= k) // 买了可乐
            {
                int j = i + 1;
                if (dis[v][j] > dis[u][i] + w)
                {
                    dis[v][j] = dis[u][i] + w;
                    q.push({dis[v][j], j, v});
                }
            }
            if (a[v] == -1 && abs(i - 1 - 10) <= k)
            {
                int j = i - 1;
                if (dis[v][j] > dis[u][i] + w)
                {
                    dis[v][j] = dis[u][i] + w;
                    q.push({dis[v][j], j, v});
                }
            }
        }
    }
    // min_element 返回最小值的迭代器（指针），通过 * 解引用获取具体的值。
    int ans = *min_element(dis[t].begin(), dis[t].end());
    if (ans == 1e9)
        ans = -1;
    cout << ans << "\n";
}
int main()
{
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}

```

</details>


## [Code+#4] 最短路


<details>

<summary>满分代码</summary>


```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int N, M, C;
    cin >> N >> M >> C;
    vector<vector<pair<int, int>>> e(N + 1);
    // 读取 M 条特殊边
    for (int i = 0; i < M; i++)
    {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].push_back({v, w});
    }
    // 普通异或边：逐位翻转。注意 0 号点也要考虑进来
    for (int i = 0; i <= N; i++)
    {
        for (int k = 0; k <= 16; k++) // n 最多 1e5，至多考虑到 2 的 16 次方即可
        {
            int j = i ^ (1 << k);
            if (j <= N)
            {
                e[i].push_back({j, (1 << k) * C});
            }
        }
    }
    int A, B;
    cin >> A >> B;
    // Dijkstra
    vector<long long> dist(N + 1, 1e18);
    vector<bool> vis(N + 1, false);
    priority_queue<pair<long long, int>, vector<pair<long long, int>>, greater<>> pq;
    dist[A] = 0;
    pq.push({0, A});
    while (!pq.empty())
    {
        auto [d, u] = pq.top();
        pq.pop();
        if (vis[u])
            continue;
        vis[u] = true;
        for (auto it : e[u])
        {
            int v = it.first, w = it.second;
            if (dist[v] > d + w)
            {
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }
    cout << dist[B] << "\n";
    return 0;
}
```

</details>


## [GXOI/GZOI2019] 旅行者


<details>

<summary>满分代码</summary>


```cpp
#include <bits/stdc++.h>
#define ll long long
#define f first
#define s second
using namespace std;
typedef pair<int, int> p;
const int N = 1e5 + 5;
ll n, m, dis[N], k, st, ed, a[N];
bool vis[N];
vector<p> e[N], t[N];
struct node
{
    ll id, d; // 编号，距离
    bool operator < (const node&x) const
    {
        return d > x.d;
    }
};
void dijkstra()
{
    priority_queue<node> q;
    memset(vis, 0, sizeof(vis));
    memset(dis, 0x7f, sizeof(dis));
    dis[0] = 0;
    q.push({0, 0});
    while (q.size())
    {
        node now = q.top();
        q.pop();
        int u = now.id, distance = now.d;
        if (vis[u]) continue;
        vis[u] = 1;
        for (auto x : e[u])
        {
            int v = x.f, w = x.s;
            if (dis[v] > dis[u] + w)
            {
                dis[v] = dis[u] + w;
                q.push({v, dis[v]});
            }
        }
    }
}
void init()
{
    for (int i = 0; i <= n + 1; i++) e[i].clear(), e[i] = t[i];
}
void solve()
{
    cin >> n >> m >> k;
    for (int i = 0; i <= n + 1; i++) e[i].clear(), t[i].clear();
    while (m--)
    {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].push_back({v, w});
        t[u].push_back({v, w});
    }
    for (int i = 1; i <= k; i++) cin >> a[i];
    ll ans = 1e18;
    for (int i = 0; (1 << i) <= n; i++)
    {
        init();
        for (int j = 1; j <= k; j++)
        {
            if (a[j] >> i & 1) e[0].push_back({a[j], 0});
            else e[a[j]].push_back({n + 1, 0});
        }
        dijkstra();
        ans = min(ans, dis[n + 1]);
        init();
        for (int j = 1; j <= k; j++)
        {
            if (a[j] >> i & 1) e[a[j]].push_back({n + 1, 0});
            else e[0].push_back({a[j], 0});
        }
        dijkstra();
        ans = min(ans, dis[n + 1]);
    }
    cout << ans << "\n";
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int t = 1;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

</details>


