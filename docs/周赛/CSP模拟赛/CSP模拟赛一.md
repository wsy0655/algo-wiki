## T1

知识点：简单循环

循环遍历枚举 $D$ 的所有可能即可。然后逐一判断是否满足 $D+D\times 10+D=n$ 即可。

```cpp
for (int D = 1; D <= 9; D++)
{
    if (D + D * 10 + D == n)
    {
        cout << D;
        return 0;
    }
}
cout << 404;
```


____

## T2

知识点：简单循环

如果个位不是 $3$ 就要一直循环迭代，根据题目的迭代规则进行编写代码即可。

```cpp
int x;
cin >> x;
int ans = 0, now = 0;
while (now % 10 != 3)
{
    now += x;
    if (x % 2 == 1)
    {
        x = x * 3 + 1;
    }
    else
    {
        x = x / 2;
    }
    ans++;
}
```


___


## T3


知识点：二维数组，枚举模拟。


由于只允许选择一个没有干员的位置放魔法，因为首先双重循环遍历所有位置 $(i,j)$。

- 若满足 $(i,j)$ 没有干员。（输入时给干员的位置打标记，用标记数组判断是否存在干员）
- 接下来需要求出 $(i,j)$ 周围有多少个干员。
    - 由于要求干员和 $(i,j)$ 的曼哈顿距离在 $2$ 以内。
    - 因此可以遍历以 $(i,j)$ 为中心的一个 $5\times 5$ 的子矩阵，然后检验距离是否在 $2$ 以内，且是否存在干员。


时间复杂度：$O(nm)$。数据范围比较小，枚举所有干员去逐一判断也可以。


```cpp
#include <bits/stdc++.h>
using namespace std;
bool vis[105][105];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n, m, k;
    cin >> n >> m >> k;
    for (int i = 0; i < k; i++)
    {
        int x, y;
        cin >> x >> y;
        vis[x][y] = 1;
    }
    int ans = 0;
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            if (vis[i][j]) continue; // 存在干员则跳过
            int sum = 0;
            for (int dx = -2; dx <= 2; dx++)
            {
                for (int dy = -2; dy <= 2; dy++)
                {
                    int nx = i + dx, ny = j + dy;
                    int dis = abs(i - nx) + abs(i - ny);
                    if (nx >= 1 && nx <= n && ny >= 1 && ny <= m && vis[nx][ny] && dis <= 2)
                        sum++;
                }
            }
            ans = max(ans, sum);
        }
    }
    cout << ans;
    return 0;
}
```


___


## T4

知识点：数组，模拟

数据范围比较小，暴力进行插入删除等操作的实现即可。

**操作一**

循环遍历求区间最大值即可，或者用 `max_element` 函数求区间最大值。

___

**操作二**

需要先将 $a[x+1]\sim a[n]$ 整体右移一位以后，在赋值 `a[x + 1] = y`。

- 注意右移需要倒着实现，核心代码为 `a[i + 1] = a[i]`。


```cpp
// a[x+1]~a[n] 往后挪一位
for (int i = n; i >= x + 1; i--)
    a[i + 1] = a[i];
// y 放在 a[x+1]
a[x + 1] = y;
// 位数 +1 
n++;
```

___

**操作三**

直接赋值修改即可。


____


**操作四**


删除区间 $[x,y]$，就好比让 $a[y+1]\sim a[n]$ 左移若干位。

```cpp
// 删了 len 个数
int len = y - x + 1;
// a[y+1]~a[n] 往前挪到 a[x] 开头的位置
// 也就是往前挪 len 位
for (int i = y + 1; i <= n; i++)
    a[i - len] = a[i];
n -= len;
```


____

这个题用 `std::vector` 可以随便做。

- 操作一使用 `max_element` 函数求区间最值，复杂度 $O(n)$。
- 操作二使用 `insert` 函数插入元素，复杂度 $O(n)$。
- 操作三直接赋值即可。
- 操作四使用 `erase` 函数删除元素，复杂度 $O(n)$。

___


## T5

知识点：排序。

排序以后从前往后扫一遍即可，扫的过程中维护一个计数变量 `cnt` 代表目前顺子的长度，若顺子长度可以达到 $k$ 则符合要求。

```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 3e5 + 5;
int a[N];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int n, k;
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
    }
    sort(a + 1, a + n + 1);
    int cnt = 1;
    for (int i = 2; i <= n; i++)
    {
        if (a[i] - a[i - 1] == 1) // 顺子累加
            cnt++;
        else if (a[i] != a[i - 1]) // 顺子断开，相等的话顺子不算断开
            cnt = 1;
        if (cnt == k)
        {
            cout << "Yes";
            return 0;
        }
    }
    cout << "No";
    return 0;
}
```


___


## T6


知识点：BFS，预处理，二分。


如果可以提前构造出 $10^{14}$ 以内所有的好数并存储起来，那么对于每次询问可以二分求出好数的位置。


那么首先要解决的是：好数多不多？

> 好数如果太多，全存起来就会 MLE。因此可以估算一下好数的数量级。


____

**估算方法**

设目前好数长度为 $L$，从高到低每一位分别设为 $a_1,a_2,\ldots,a_L$.

先研究非降序列 $a$ 的方案数。

设差分序列 $b$ 满足 $b_i=a_{i}-a_{i-1}$。则差分性质可得：

$$\begin{cases}
b_1\geq 1\\
b_2\sim b_L\geq 0\\
b_1+b_2+\cdots+b_L=a_L\leq 9
\end{cases}$$

令 $b_1'=b_1-1$，则 $b_1'\sim b_L\geq 0$。

此时我们要研究的就是

$$\begin{cases}
b_1'\sim b_L\geq 0\\
b_1'+b_2+\cdots+b_L=a_L\leq 8
\end{cases}$$

这个序列的方案数，使用插板法可以推出为 $\binom{L+8}{8}$。

根据数据范围极限设 $L=13$，则方案数为 $203490$。

$13$ 位数的好数个数粗略可以估计为这么多（没有算非升序列对称的差不多）。


所以其实 $10^{14}$ 以内不会有太多的好数。


____


**难点二：如何预处理所有好数**

显然不能枚举 $1\sim 10^{14}$ 的所有数，否则会超时。


考虑使用构造法来推进所有好数。例如一个两位好数可以看作是一位好数拼接一位得到，初始化所有一位好数记作 $i$，其中 $i\in [1,9]$。

那么接下来可以构造所有两位好数，构造两位以后就可以构造三位。整个过程像一棵搜索树，使用 BFS 可以构造所有好数并存储起来。


最后二分即可。


____



**DFS做法**

常数大一些，主要是重复的数字处理。

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
vector<ll> good;
// 生成非降
void dfs1(int pos, int len, int last, ll cur)
{
    if (pos > 0)
        good.push_back(cur);
    if (pos == len)
        return;
    for (int d = last; d <= 9; d++)
    {
        dfs1(pos + 1, len, d, cur * 10 + d);
    }
}
// 生成非增
void dfs2(int pos, int len, int last, ll cur)
{
    if (pos > 0 && cur > 0)
        good.push_back(cur);
    if (pos == len)
        return;
    for (int d = last; d >= 0; d--)
    {
        dfs2(pos + 1, len, d, cur * 10 + d);
    }
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    // 生成所有好数（长度 ≤ 14）
    for (int len = 1; len <= 14; len++)
    {
        dfs1(0, len, 1, 0); // 非降，首位≥1
        dfs2(0, len, 9, 0); // 非增，首位≤9
    }
    // 特殊处理 10^14
    good.push_back((ll)1e14);
    sort(good.begin(), good.end());
    good.erase(unique(good.begin(), good.end()), good.end());
    int T;
    cin >> T;
    while (T--)
    {
        ll n;
        cin >> n;
        // 找到 n 在 good 中的排名（1-based）
        int idx = lower_bound(good.begin(), good.end(), n) - good.begin();
        cout << idx + 1 << "\n";
    }
    return 0;
}
```

____



**BFS做法**

注意实现姿势，保证有序生成和重复的问题，降低实现常数。

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    vector<ll> good;
    queue<pair<ll, int>> q;
    for (int d = 1; d <= 9; d++)
    {
        q.push({d, 2});
        good.push_back(d);
    }
    while (!q.empty())
    {
        auto it = q.front();
        q.pop();
        ll num = it.first;
        int state = it.second;
        if (num > 1e14)
            continue;
        int last = num % 10; // 个位
        if (state == 2)
        {
            // 转非增
            for (int d = 0; d < last; d++)
            {
                ll nxt = num * 10 + d;
                q.push({nxt, 1});
                good.push_back(nxt);
            }
            // 继续相等
            ll nxt = num * 10 + last;
            q.push({nxt, 2});
            good.push_back(nxt);

            // 转非降
            for (int d = last + 1; d <= 9; d++)
            {
                ll nxt = num * 10 + d;
                q.push({nxt, 0});
                good.push_back(nxt);
            }
        }
        else if (state == 0)
        {
            // 非降
            for (int d = last; d <= 9; d++)
            {
                ll nxt = num * 10 + d;
                q.push({nxt, 0});
                good.push_back(nxt);
            }
        }
        else
        {
            // 非增
            for (int d = 0; d <= last; d++)
            {
                ll nxt = num * 10 + d;
                q.push({nxt, 1});
                good.push_back(nxt);
            }
        }
    }
    int T;
    cin >> T;
    while (T--)
    {
        ll n;
        cin >> n;
        int idx = lower_bound(good.begin(), good.end(), n) - good.begin();
        cout << idx + 1 << "\n";
    }
    return 0;
}
```


____


## T7

知识点：动态规划，图论建模

原题：abc341F


**子任务2**


给定的关系是一棵树，而且是一个链状的，说白了就是一个 $1\sim n$ 的序列，我们直接按照序列的思考方式来思考这个题怎么做即可。

手玩几个例子就可以发现做法了，由于此时相邻关系非常有限。所以首先按照 $w_i$ 从小到大排序所有棋子。

例如有 $3$ 个棋子，初始每个位置有 $1,2,3$ 个棋子，且满足 $w_1<w_2<w_3$。

那么此时不难发现答案就是 $w_3+(w_3+w_2)+(w_3+w_2+w_1)$。

也就是一旦遇到 $w_i=w_{i-1}$ 的棋子后，就会断开不能把棋子贡献过去。因此倒序扫一遍，维护前缀和即可。


```cpp
cin >> n >> m;
for (int i = 1; i <= n; i++)
    cin >> a[i].first;
for (int i = 1; i <= n; i++)
    cin >> a[i].second;
sort(a + 1, a + 1 + n);
while (m--)
{
    int u, v;
    cin >> u >> v;
}
int pre = a[n].second; // 初始化第一个位置的所有棋子
int ans = pre;
for (int i = n - 1; i >= 1; i--)
{
    if (a[i] < a[i + 1])
    {
        pre += a[i].second;
        ans += pre;
    }
    else
    {
        pre = a[i].second;
        ans += pre;
    }
}
cout << ans;
```


___



**满分解法**


对于一条边 $(u,v)$，若 $u$ 让 $v$ 的麻烦加 $1$，必须满足 $a_u>a_v$。因此连接关系其实是单向的，整个图最终一定可以构成 DAG。这启发我们用 DP 解决该问题。

每个麻烦都是独立的，因此我们可以求出每个点只有 $1$ 个麻烦时最多操作多少次，然后乘以 $b_i$ 即为该点操作的答案。

则答案应该是所有点的操作次数总和。

定义 $dp_u$ 为以 $u$ 为起点出发可以操作的次数总和，如果没有体重限制则

$$
dp_u=1+\sum_{v\in e[u]} dp_v
$$

其中 $e[u]$ 为 $u$ 的所有邻点集合。


但由于有体重的限制，我们只能选择若干个邻点 $v$ 来进行最终的抉择，也就是每个邻点 $v$ 选或不选的问题，由于体重 $\leq 5000$，因此可以用 $[0-1]$ 背包的方式求解 

$$
\max(\sum_{v\in e[u]} dp_v\cdot [s[v] <a[u]])
$$

其中 $s[v]$ 代表选择的邻点 $v$ 的体重之和，必须小于 $a_u$。


最后注意记忆化搜索，避免对一个点重复搜。这里我们采取 DFS 实现拓扑序 DP 问题。


```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
constexpr int N = 5e3 + 5;
ll n, m, u[N], v[N], b[N], a[N], dp[N];
vector<int> e[N];
ll dfs(int u)
{
    if (~dp[u]) return dp[u];
    for (auto v : e[u])
    {
        dp[v] = dfs(v); // 获取邻点 v 的最大操作次数保存到 dp[v]
    }
    // 由于所有邻点 v 或许不能都选上，因此做 [0 - 1] 背包求最大值
    vector<int> f(a[u] + 1); // 创建一个容量为 a[u] 的数组 f
    for (auto v : e[u]) // 遍历所有儿子 v
    {
        for (int j = a[u]; j >= a[v]; j--) // 倒序 0 - 1 背包
        {
            // 物品重量：a[v]，物品价值：dp[v] 
            f[j] = max(f[j], f[j - a[v]] + dp[v]);
        }
    }
    // 所有儿子的重量之和最多是 a[u] - 1
    return dp[u] = f[a[u] - 1] + 1;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> a[i];
    for (int i = 1; i <= n; i++) cin >> b[i];
    for (int i = 1; i <= m; i++)
        cin >> u[i] >> v[i];
    // 根据体重关系建立拓扑图
    for (int i = 1; i <= m; i++)
    {
        if (a[u[i]] > a[v[i]])
            e[u[i]].push_back(v[i]);
        else if (a[u[i]] < a[v[i]])
            e[v[i]].push_back(u[i]);
    }
    memset(dp, -1, sizeof(dp));
    for (int i = 1; i <= n; i++) dfs(i);
    int ans = 0;
    for (int i = 1; i <= n; i++) ans += dp[i] * b[i];
    cout << ans;
    return 0;
}
```