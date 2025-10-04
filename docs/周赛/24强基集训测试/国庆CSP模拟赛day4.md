

## T1 镜子（mirror）

**知识点：数学，贪心。**

沿数轴，关于点 $p$ 的“镜像”是线性变换 $f(a)=2p-a$。例如对三个镜子按顺序在 $(x,y,z)$ 处做变换，有

\[
2z-\bigl(2y-(2x-a)\bigr)=2(z-y+x)-a.
\]

推广到 $n$ 个镜子，最终形态为

\[
F(a)=\bigl(\pm a\bigr)+2(\pm x_1 \pm x_2 \pm \cdots \pm x_n),
\]


其中符号“奇加偶减”交替：即第 $1$、$3$、$5\ldots$ 个镜子对应的系数为 $+$，第 $2$、$4$、$6\ldots$ 为 $-$。

因此：

- **$n$ 为奇数**：系数为 $-a$，即“奇加偶减交替 **减去*- 固定的 $a$”；
- **$n$ 为偶数**：系数为 $+a$，即“奇加偶减交替 **加上*- 固定的 $a$”。

要使结果尽量大，只需让**被加的项尽量大、被减的项尽量小**。将所有镜子位置排序后，把较小的一半放到“减”的位置、较大的一半放到“加”的位置。最后再按 $n$ 的奇偶决定是 $-a$ 还是 $+a$。

- 时间复杂度：排序占主导，为 $O(n\log n)$。

**实现步骤**

1. 将镜子位置数组 $a[1..n]$ 从小到大排序；
2. 设 `ans = 0`；
3. 对 $i=1.. \lfloor n/2 \rfloor$：`ans -= a[i]`；
4. 对 $i=\lfloor n/2 \rfloor+1 .. n$：`ans += a[i]`；
5. 若 $n$ 为奇数，答案为 `2 * ans - a0`；若为偶数，答案为 `2 * ans + a0`（其中 `a0` 是初始位置 $a$，用单独变量存）。


---

## T2 蛋糕（cake）

**知识点：二分，前缀和。**

每块蛋糕在按“均分为两半”的规则切分时，最多切 $\log_2 x$ 次；最终会被切成**相等大小**的若干块。

把每块蛋糕的大小 $x$ 写成 $x = 2^k\cdot v$（其中 $v$ 为奇数），那么这块蛋糕最终会变成 **$2^k$ 块、每块大小为 $v$**。

预处理每块蛋糕的「块数」与「单块大小」：

```cpp
int x;
cin >> x;
int cnt = 1;      // 最终会切成的块数 = 2^k
while (x % 2 == 0) 
{
    cnt <<= 1;
    x >>= 1;
}
a[i] = {cnt, x};  // x 现在就是奇数 v
```

随后对「块数」做前缀和 `pre[i] = pre[i - 1] + a[i].cnt`。

**回答查询**：

做法：在前缀和 `pre` 上用 `lower_bound` 找到第一个使 `pre[pos] >= t` 的下标 `pos`，答案即为 `a[pos].v`。

- 预处理复杂度：每个 $x$ 只要把因子 $2$ 除光，整体 $O(n\log x)$；
- 查询复杂度：每次二分 $O(\log n)$，故总 $O(n\log x + q\log n)$。



---

## T3 序列（seq）

**知识点：位运算，前缀和。**

- 初始给定数组 $a[1..n]$；
- **操作 1**：把**全体元素**做一次按位或：`a[i] |= x`（全局 OR，且可以多次，等价于维护一个累积掩码 `mask |= x`）；
- **操作 2**：查询区间和 $\sum_{i=l}^r a[i]$。

关键观察：OR 操作**不会**把某一位的 $1$ 变成 $0$。维护每一位的前缀和即可高效回答。

**预处理**

对每一位 $j\in[0,29]$，维护「该位贡献的前缀和」：

```cpp
for (int i = 1; i <= n; i++) 
{
    for (int j = 0; j < 30; j++) 
    {
        if (a[i] >> j & 1)
            sum[i][j] = sum[i - 1][j] + (1 << j);
        else
            sum[i][j] = sum[i - 1][j];
    }
}
```

**维护全局 OR**

把所有“操作 1”的影响用一个整型 `mask` 累加即可：每次操作执行 `maks |= x` 即可。

- 若在某位 $j$ 上 `mask` 的该位为 1，则**当前所有元素**该位都为 $1$；
- 若为 $0$，则该位保持为初始数组对应的样子。

**回答区间和**

对每一位 $j$：

- 若 `mask` 在二进制第 $j$ 位上是 $1$，则该位在 $[l,r]$ 的贡献为 $(r-l+1)\cdot 2^j$；
- 否则贡献为 `sum[r][j] - sum[l - 1][j]`。

总复杂度：

- 预处理 $O(n\cdot B)$；
- 每次查询 $O(B)$，其中 $B=30$。


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 2e5 + 5;
long long sum[N][30];  // 前缀和：每一位的权值贡献
int a[N];
int main() 
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int n, q;
    cin >> n >> q;
    for (int i = 1; i <= n; ++i) 
    {
        cin >> a[i];
        for (int j = 0; j < 30; ++j) 
            sum[i][j] = sum[i - 1][j] + ((a[i] >> j & 1) ? (1 << j) : 0);
    }
    long long mask = 0;  // 累积全局 OR
    while (q--) 
    {
        int op; 
        cin >> op;
        if (op == 1) 
        {
            int x; 
            cin >> x;
            mask |= x;  // 全局 OR 累积
        } 
        else 
        {
            int l, r; 
            cin >> l >> r;
            long long ans = 0;
            int len = r - l + 1;
            for (int j = 0; j < 30; ++j) 
            {
                if (mask >> j & 1) 
                    ans += 1ll * (1 << j) * len;
                else 
                    ans += sum[r][j] - sum[l - 1][j];
            }
            cout << ans << '\n';
        }
    }
    return 0;
}
```

---

## T4 机器人（robot）

知识点：最短路，拆点。



**测试点 $9\sim 12$**

该部分满足特殊性质 $A$，特殊性质 $A$ 机器人转方向没有代价，则只需要考虑正常的边权代价。因此直接使用 Dijkstra 求到每个点的最短路即可得到这 $20$ 分。



___

**测试点 $1\sim 5$**

考虑暴力 DFS。


- 我们把**机器人的当前状态**定义为二元组 **$(u, p)$**：
    - `u`：所在路口；
    - `p`：机器人的参数值（$1\ldots k$）。
- 从一个状态可以做三类事情（都有非负代价）：
    - **改参：**
         - 若 `p < k`，花费 `v[p]`，到 **$(u, p+1)$**；
         - 若 `p > 1`，花费 `w[p]`，到 **$(u, p−1)$**。
    - **走路：**
         - 若 `u` 的出边数 $\geq$ `p`，可以沿**第 $p$ 条出边**走到它的终点 `v`（这条边的权值是 `w`），得到 **$(v, p)$**，花费 `w`。
- 目标：从初始状态 **$(1, 1)$** 出发，求到每个路口 `u` 的最小花费（不关心最终 `p` 是多少）。

这就是一个有向图（状态图）上的最短路问题：图的结点是所有可达的 `(u, p)`，边是上面三类动作。答案是对每个 `u`，所有状态 `(u, p)` 中的最小到达代价。

---


- 维护一个二维数组 `dis[u][p]`，表示我们**目前已知**到达状态 `(u, p)` 的最小花费；初始化为无穷大。
- 每当我们用某条路径来到 `(u, p)`，若新花费 `cost` **没有更小**，就剪枝返回；否则更新 `dis[u][p] = cost`，再从这里**继续扩展三类动作**。
- 因为**所有动作代价都非负**，且每次“找到更短路”才会继续往外扩，所以：
    - 不会出现“来回抖动把 `dis` 越改越小”的负环问题；
    - 每个 `(u, p)` 的 `dis` 值最多被改“若干次”（从 $+\infty$ 逐步下降到最短路），总体是**有限次**，所以**一定会收敛**。
- 这就像一个顺序不固定的“最短路松弛过程”，**正确性没问题**，只是**效率差**：可能要很多次递归与回溯，**仅适合小数据**。

---

**具体维护**

- `dis[u][p]`：到达状态 `(u, p)` 的**最小花费**（用于剪枝+继续扩展）。
- `ans[u]`：到达路口 `u` 的**最小花费**（用于输出）。
    - 每次更新了 `dis[u][p]`，就顺便 `ans[u] = min(ans[u], dis[u][p])`。
- `adj[u]`：按输入顺序保存 `u` 的每条出边（第 $1$、$2$、$3\ldots$ 条），这样我们可以直接用 `adj[u][p-1]` 访问“第 $p$ 条边”。

---

**DFS 的三类“转移”写成代码**

在 `dfs(u, p, cost)` 中：

- **剪枝：** `if (cost >= dis[u][p]) return;` 不是更优，直接停。

- **更新：** 
    - `dis[u][p] = cost;`
    - `ans[u] = min(ans[u], cost);`

- **三类扩展：**
     - 改参 `p -> p+1`（若 `p < k`）：`dfs(u, p + 1, cost + v[p]);`
     - 改参 `p -> p - 1`（若 `p > 1`）：`dfs(u, p - 1, cost + w[p]);`
     - 走第 `p` 条边（若 `p <= adj[u].size()`）：`auto [v, w] = adj[u][p - 1];` 则 `dfs(v, p, cost + w);`

> 关键点：**“走路”只能走第 $p$ 条边**，这就是题目的特别之处；改参能让“可走的边编号”变多或变少。

---


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 3e5 + 5;
int c, n, m, k, v[N], w[N];
vector<pair<int, int>> adj[N];
int dis[1005][1005]; // dis[u][p]：到达状态 (u,p) 的最小花费
int ans[N]; // ans[u]：到达路口 u 的最小花费

void dfs(int u, int p, int cost)
{
    if (cost >= dis[u][p]) return ; // 剪枝
    dis[u][p] = cost;
    ans[u] = min(ans[u], cost);

    // 操作 1：p 增加 1
    if (p < k)
    {
        int np = p + 1;
        int ncost = cost + v[p];
        if (ncost < dis[u][np])
            dfs(u, np, ncost);
    }

    // 操作 2：p 减少 1
    if (p > 1)
    {
        int np = p - 1;
        int ncost = cost + w[p];
        if (ncost < dis[u][np])
            dfs(u, np, ncost);
    }

    // 走第 p 条边（若存在）
    if (p >= 1 && p <= (int)adj[u].size())
    {
        auto [v, w] = adj[u][p - 1]; // p 是从 1 开始编号
        int ncost = cost + w;
        if (ncost < dis[v][p])
            dfs(v, p, ncost);
    }
}
signed main()
{
    freopen("robot.in", "r", stdin);
    freopen("robot.out", "w", stdout);
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> c;
    cin >> n >> m >> k;
    for (int i = 1; i <= k - 1; ++i)
        cin >> v[i];
    for (int i = 2; i <= k; ++i)
        cin >> w[i];
    for (int i = 1; i <= n; ++i)
    {
        int d;
        cin >> d;
        for (int j = 1; j <= d; ++j)
        {
            int y, z;
            cin >> y >> z;
            adj[i].push_back({y, z});
        }
    }
    memset(dis, 0x3f, sizeof(dis));
    memset(ans, 0x3f, sizeof(ans));

    // 初始在路口 1，p=1，花费 0
    dfs(1, 1, 0);
    // 输出每个点的最小花费，不可达输出 -1
    for (int i = 1; i <= n; ++i)
    {
        if (ans[i] == 0x3f3f3f3f3f3f3f3f)
            cout << -1 << " ";
        else
            cout << ans[i] << " ";
    }
    return 0;
}
```




___


**满分解法**




**1. 把问题建成“状态图”的最短路**

* 定义**状态**为 $(u,p)$：在路口 $u$、参数为 $p$。
* 从 $(u,p)$ 出发，有三类**非负代价**的动作：
    - **加参**：若 $p<k$，以代价 $v_p$ 到 $(u,p+1)$；
    - **减参**：若 $p>1$，以代价 $w_p$ 到 $(u,p-1)$；
    - **走路**：若 $u$ 的出边条数 $\ge p$，可沿“第 $p$ 条边”$(u \to v, \text{len}=z)$ 到 $(v,p)$，代价 $z$。

题目要求的是“到达每个路口 $i$ 的最小费用”（不关心最终 $p$），所以我们在最短路过程中**一旦走到某个路口 $v$**，就可用当前代价去**更新**答案 $ans[v]$。

由于所有代价非负，直接在这张状态图上用 **Dijkstra** 即可得到最优解。

---

**2. 为什么不用 $n\times k$ 的数组，而用每个点一个 `map`？**

- 各点出度不同，真正“有用”的 $p$ 往往**不需要很大**：
    - 在路口 $u$，若 $p>d_u$（出边不够 $p$ 条），下一步**根本不能走路**，总要先把 $p$ 减到 $\le d_u$才能前进；
    - 换句话说，在 $u$ 只需要关注 $p\in[1,d_u]$（以及从别处刚到 $u$ 时可能暂时更大的 $p$，但会**立即压回** $d_u$）。
- 因为 $\sum d_i = m$，整体“必要的第二维状态总量”$\approx O(m)$。
    - 用 `map<int,int> dis[u]` 按需开状态，空间更省，时间的对数因子也可接受。

---

**3. 调参费用做**前缀和**，支持“批量压回/拉起”**

设：

* $A[i]=\sum_{t=1}^{i} v_t$（$(i=1..k-1)$）；
* $B[i]=\sum_{t=2}^{i} w_t$（$i=2..k$）。

则有：

* **一步加参** $p\to p+1$ 的费用 = $A[p]-A[p-1]=v_p$；
* **一步减参** $p\to p-1$ 的费用 = $B[p]-B[p-1]=w_p$。

更关键的是，当我们**移动到新路口 $v$** 后：

* 若当前 $p\le d_v$，状态 $(v,p)$ 直接合法；
* 若当前 $p>d_v$，我们可以 **一次性** 把 $p$ 压到 $d_v$，总费用就是 $B[p]-B[d_v] = w_{d_v+1} + \cdots + w_p$。
  
这一步把“多次 $p\to p-1$”合并成“一次”，**减少边数与状态数**，加速很多。

---

**4. Dijkstra 的节点与边**

* 堆中元素是三元组 $(\text{dist},u,p)$，表示当前已知到达 $(u,p)$ 的最小代价；
* 出堆后（首次出堆即最优），从 ((u,p)) 进行三类松弛：
    - 若 $p<d_u$：尝试 $(u,p)\to(u,p+1)$，费用 $v_p$；
    - 若 $p>1$：尝试 $(u,p)\to(u,p-1)$，费用 $w_p$；
    - 若 $p\le d_u$：可走“第 $p$ 条边”到 $v$，
         - 先用 $\text{dist}+z$ 更新 $ans[v]$；
         - 若 $p\le d_v$：松弛 $(v,p)$；
         - 否则 $p>d_v$：一次性减到 $d_v$，额外费用 $B[p]-B[d_v]$，松弛 $(v,d_v)$。

> 这保证了：在任何路口 $u$，我们不会“无意义地把 $p$ 提得比 $d_u$ 还高”，也不会“在 $v$ 处一层层慢慢把 $p$ 压回去”。状态和边都被**合理压缩**。

---

**5. 正确性小结**

* 我们等价在“所有 $(u,p)$”上跑最短路，但把“显然无效/低效的调参路径”合并（批量压回），不会漏掉最优解；
* 所有边权非负，Dijkstra 正确；
* 只要一到达某个路口 $v$，该时刻的总代价就是一条从 $(1,1)$ 到 $v$ 的可行方案的代价，取最小即为答案。

---

**6. 复杂度**

* 有效状态数约为 $\sum_u O(d_u)=O(m)$ 量级；
* 每个状态的出边常数条，Dijkstra 复杂度

\[
O\big((n+m)\log(n+m)\big),
\]

可以覆盖题目最大数据范围。

---

**参考实现**



```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
using pii = pair<int, int>;
using ary = array<int, 3>;
constexpr int N = 3e5 + 5;
int n, m, k, a[N], b[N], d[N], ans[N];
vector<pii> e[N];
map<int, int> dis[N];
map<int, bool> vis[N];
void init(int u, int p)
{
    if (!dis[u].count(p))
        dis[u][p] = 1e18;
}
signed main()
{

    ios::sync_with_stdio(false), cin.tie(0);
    int c;
    cin >> c;
    cin >> n >> m >> k;
    for (int i = 1; i < k; i++)
    {
        cin >> a[i];
        a[i] += a[i - 1];
    }
    for (int i = 2; i <= k; i++)
    {
        cin >> b[i];
        b[i] += b[i - 1];
    }
    for (int i = 1; i <= n; i++)
    {
        cin >> d[i];
        ans[i] = 1e18;
        for (int j = 1; j <= d[i]; j++)
        {
            int v, w;
            cin >> v >> w;
            e[i].push_back({v, w});
        }
    }
    priority_queue<ary, vector<ary>, greater<ary>> q;
    q.push({0, 1, 1});
    dis[1][1] = 0;
    ans[1] = 0;
    while (q.size())
    {
        auto [dist, u, p] = q.top();
        q.pop();
        if (vis[u][p]) continue;
        vis[u][p] = 1;
        
        auto [v, w] = e[u][p - 1];
        ans[v] = min(ans[v], dist + w);

        
        if (p < d[u])
        {
            int w = a[p] - a[p - 1];
            init(u, p + 1);
            if (dis[u][p + 1] > dis[u][p] + w)
            {
                dis[u][p + 1] = dis[u][p] + w;
                q.push({dis[u][p + 1], u, p + 1});
            }
        }

        if (p > 1)
        {
            int w = b[p] - b[p - 1];
            init(u, p - 1);
            if (dis[u][p - 1] > dis[u][p] + w)
            {
                dis[u][p - 1] = dis[u][p] + w;
                q.push({dis[u][p - 1], u, p - 1});
            }
        }

        if (p <= d[v])
        {
            init(v, p);
            if (dis[v][p] > dis[u][p] + w)
            {
                dis[v][p] = dis[u][p] + w;
                q.push({dis[v][p], v, p});
            }
        }
        else 
        {
            if (d[v])
            {
                w += b[p] - b[d[v]];
                init(v, d[v]);
                if (dis[v][d[v]] > dis[u][p] + w)
                {
                    dis[v][d[v]] = dis[u][p] + w;
                    q.push({dis[v][d[v]], v, d[v]});
                }
            }
        }
    }
    for (int i = 1; i <= n; i++)
    {
        if (ans[i] == 1e18) ans[i] = -1;
        cout << ans[i] << " ";
    }
    return 0;
}
```

