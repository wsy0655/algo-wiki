## T1

```cpp
int a, b;
cin >> a >> b;
if (a > b) b += 12;
cout << b - a; 
```

___


## T2


知识点：思维数学。

首先答案只有两种情况：

- 令 $a,c$ 同时增大到 $b+1$。
- 令 $b$ 减小到 $\min(a,c)-1$。

注意情况二可能无法满足，例如 $\min(a,c) =1$ 时，题目不允许 $b=0$ 的情况出现。因此需要一些特判。


```cpp
int a, b, c;
cin >> a >> b >> c;
int sum1 = max(0, b - a + 1) + max(0, b - c + 1);
int sum2 = max(0, b - min(a, c));
if (min(a, c) != 1) 
    sum2++;
if (a == 1) 
    sum2++;
if (c == 1) 
    sum2++;
cout << min(sum1, sum2) << '\n';
```


___


## T3



**核心观察**

* 题目要求：对每个 $b_i$，必须满足 **对所有 $a_j$ 都有 $b_i \& a_j = a_j$**。
* 这意味着：
  $a_j$ 在二进制中为 `1` 的每一位，$b_i$ 在这一位上也必须是 `1`。
  换句话说，$a_j$ 是 $b_i$ 的「子集」。

---

**化简条件**

* $b_i$ 需要同时包含所有 $a_j$ 的 `1` 位。
* 因此，只要 $b_i$ 覆盖了 **所有 $a_j$ 的并集** 就可以了。
* 所谓并集，就是把所有 $a_j$ 的二进制 `1` 位取个总和（按位或）：

  $$
  U = a_1 \;|\; a_2 \;|\; \dots \;|\; a_n
  $$

* 那么条件就变成：

  $$
  b_i \& U = U
  $$

  也就是说，$b_i$ 至少要有 $U$ 中的所有 `1` 位。

---

**算法流程**

1. 读入数组 $a$ 和 $b$。
2. 用按位或算出所有 $a_j$ 的并集 $U$。
3. 对每个 $b_i$ 检查：

   * 如果 $b_i \& U = U$，则说明 $b_i$ 合格。
4. 统计合格的 $b_i$ 个数，输出即可。

---

**复杂度分析**

* 第一步合并所有 $a_j$：$O(n)$。
* 第二步检查所有 $b_i$：$O(m)$。
* 总复杂度 $O(n+m)$，可以轻松应对 $n,m \leq 10^5$。



```cpp
int n, m;
cin >> n >> m;
int U = 0;
for (int i = 0; i < n; i++)
{
    int x;
    cin >> x;
    U |= x;
}
int sum = 0;
for (int i = 0; i < m; i++)
{
    int x;
    cin >> x;
    if ((x & U) == U)
    {
        sum++;
    }
}
cout << sum;
```


___


## T4

知识点：预处理前缀和


粗略的计算一下可以发现 $1970-1-1\sim 2025-12-31$ 之间的天数大约数量级为 $3\times 10^6$，因此可以预处理每一个日期属于第几天，然后使用前缀和就可以 $O(1)$ 查询了。


具体实现：

- 定义三维数组 `int a[10005][13][32]` 存储每一个日期属于第几天，`a[i][j][k]` 表示 `i` 年 `j` 月 `k` 日是第几天。
- 定义数组 `int sum[3000005]` 预处理休息日个数的前缀和。
- 维护变量 `cnt` 记录当前日期是第几天。
- 维护变量 `t` 记录当前是星期几。如果 $t\geq 3$ 则为休息日。
- 接下来从 $1970-1-1$ 日开始模拟到 $9999-12-31$ 日，每模拟一天，判断当前日期是否是休息日，如果是，则将 `sum[cnt] = sum[cnt - 1] + 1`。
- 注意闰年对 $2$ 月份带来的影响。

```cpp
#include <bits/stdc++.h>
using namespace std;

int a[10005][13][32], sum[3000005], cnt;
int y = 1970, m = 1, d = 1, t = 4;
int b[13] = {0, 31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
bool leap(int y)
{
    return y % 4 == 0 && y % 100 != 0 || y % 400 == 0;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    while (true)
    {
        cnt++;
        a[y][m][d] = cnt;
        sum[cnt] = sum[cnt - 1] + (t >= 3);
        if (y == 9999 && m == 12 && d == 31) break;
        t++;
        if (t > 7) 
            t = 1; // 回到星期一
        // 对日期处理
        d++; // 天数加 1
        // 天数加 1 可能造成月份加 1
        if (m == 2)
        {
            if (d > b[2] + leap(y))
            {
                d = 1;
                m++; // 进入到 3 月 1 日
            }
        }
        else
        {
            if (d > b[m])
            {
                d = 1;
                m++;
                if (m > 12)
                {
                    y++;
                    m = 1;
                }
            }
        }
    }
    int T;
    cin >> T;
    while (T--)
    {
        int x1, y1, z1, x2, y2, z2;
        cin >> x1 >> y1 >> z1 >> x2 >> y2 >> z2;
        int l = a[x1][y1][z1], r = a[x2][y2][z2];
        cout << sum[r] - sum[l - 1] << '\n';
    }
    return 0;
}
```

接下来在查询时首先获取两个日期对应的编号，然后使用前缀和数组直接查询休息了多少天即可。


___



## T5

知识点：数学，枚举。


对于先进行加法，再进行乘法的操作，我们可以将顺序颠倒，将它变为先乘法，再加法的操作。

例如：


$$
(x+a)\times b=x\times b+\underbrace{a+a+a+\ldots+a}_{b个}
$$

这样子，我们就可以将任意顺序、任意次数的操作转化为先乘再加了。


因此我们之需要枚举做了几次乘以 $b$ 操作，然后判断剩下的数字是不是 $a$ 的倍数即可。

注意特判：$b=1$ 的情况。

时间复杂度：$O(t\log{\text{pos}})$。

```cpp
int a, b, pos;
cin >> a >> b >> pos;
if (b == 1)
{
    if ((pos - 1) % a == 0)
    {
        cout << "YES\n";
    }
    else
    {
        cout << "NO\n";
    }
    continue;
} 
int now = 1;
bool ok = 0;
while (now <= pos)
{
    if ((pos - now) % a == 0)
    {
        ok = 1;
        break;
    }
    now *= b;
}
```


___


## T6


知识点：构造，思维。


注意到子任务 $1$ 和子任务 $2$ 对于操作次数都比较宽松，因此有一个简单的构造方法：


- 令 $i\leftarrow \lceil\frac{i}{i+1}\rceil$，其中 $i\in [2,n-2]$，这样可以将 $1\sim n-2$ 都变为 $1$。
- 接下来令 $n\leftarrow \lceil\frac{n}{n-1}\rceil$ 这样可以使得 $n$ 变为 $2$。
- 那么此时序列还剩一个 $n-1$，将其变为 $1$ 即可。
    - 只需要令 $n-1$ 一直除以右边的 $2$ 向上取整，可以证明在 $\log{n}$ 次内变为 $1$。
- 构造完成。    
- 子任务 $1$ 和 $2$ 的操作次数都是支持以上构造的。

```cpp
int n, m;
cin >> n >> m;
// 先写个子任务 1 和 2 试试
vector<pair<int, int>> ans;
for (int i = 2; i <= n - 2; i++)
{
    ans.push_back({i, i + 1});
}
ans.push_back({n, n - 1}); // 让 a[n] 变为 2
int now = n - 1;
while (now != 1)
{
    ans.push_back({n - 1, n});
    now = (now + 1) / 2;
}
cout << ans.size() << "\n";
for (auto [p, q] : ans) cout << p << " " << q << "\n";
```


子任务 $3$ 的操作次数十分有限，因此需要换一个思路。这里主要是注意到一直对 $n$ 开根号即可在有限次以内变为 $1$。


在具体实现上有一些细节，可以参考代码：


```cpp
int n, m;
cin >> n >> m;
vector<pair<int, int>> ans;
int now = n;
// 先把 3 ~ n - 1 都变为 1，变的途中对 n 开方处理。
for (int i = n - 1; i >= 3; i--)
{
    if ((now + i - 1) / i >= i)
    {
        ans.push_back({n, i});
        now = (now + i - 1) / i;
    }
    ans.push_back({i, n});
}
while (now != 1)
{
    ans.push_back({n, 2});
    now = (now + 1) / 2;
}
cout << ans.size() << "\n";
for (auto [p, q] : ans) cout << p << " " << q << "\n";
```


___


## T7

知识点：二分，最短路。


对于任意一个点 $i$，定义 $dis1_i$ 为 $s\to i$ 的最短路，$dis2_i$ 为 $t\to i$ 的最短路。

显然可以使用两次 `dijkstra` 预处理出 $dis1_i$ 和 $dis2_i$。


那么对于任意两点 $i,j$ 说考虑在它们之间新建一条长度为 $L$ 的边以后依然符合要求则

$$
dis1_i+L+dis2_j\leq k
$$

考虑枚举 $i$，则整理得到

$$
dis2_j\leq k-dis1_i-L
$$

显然 $dis2_j$ 可以排序后，使用 `upper_bound()` 找到最大的 $j$ 使得上式子依然成立。


> 特别地：如果 $dis1_t\leq k$ 则随便修，答案直接输出 $\frac{n\cdot(n-1)}{2}$ 即可。


```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using pii = pair<int, int>;
using pli = pair<i64, int>;
constexpr int N = 2e5 + 5,;
i64 n, m, s, t, l, k, dis1[N], dis2[N];
vector<pii> e[N];
void dijkstra(int s, i64 dis[])
{
    priority_queue<pli, vector<pli>, greater<pli>> q;
    q.push({0, s});
    vector<bool> vis(n + 1, 0);
    for (int i = 1; i <= n; i++) dis[i] = 1e18;
    dis[s] = 0;
    while (!q.empty())
    {
        auto [d, u] = q.top();
        q.pop();
        if (vis[u]) continue;
        vis[u] = 1;
        for (auto [v, w] : e[u])
        {
            if (dis[v] > dis[u] + w)
            {
                dis[v] = dis[u] + w;
                q.push({dis[v], v});
            }
        }
    }
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m >> s >> t >> l >> k;
    while (m--)
    {
        int u, v, w;
        cin >> u >> v >> w;
        e[u].push_back({v, w});
        e[v].push_back({u, w});
    }
    dijkstra(s, dis1), dijkstra(t, dis2);
    if (dis1[t] <= k)
    {
        cout << n * (n - 1) / 2;
        return 0;
    }
    i64 ans = 0;
    sort(dis2 + 1, dis2 + n + 1);
    k -= l;
    for (int i = 1; i <= n; i++)
    {
        int j = upper_bound(dis2 + 1, dis2 + n + 1, k - dis1[i]) - dis2 - 1;
        ans += j;
    }
    cout << ans;
    return 0;
}
```


