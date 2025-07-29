## T1

知识点：字符串，数学。


注意到输入的数字长度非常大，显然需要使用字符串存储输入的数字。

由于数字的长度最多为 $10^5$，因此无法计算出所有数位的总乘积。（存不下）



题目只需要判断乘积和 $k$ 比较，注意到 $k\leq 10^9$，因此一边乘一边判断即可。


细节：若字符串 `s` 存在字符 $0$，虽然可能前面的字符先乘起来会超过 $k$，但最终乘以 $0$ 都会得到 $0$。


```cpp
int n, k;
cin >> n >> k;
while (n--)
{
    string s;
    cin >> s;
    int sum = 1;
    bool ok = 0;
    for (char c : s)
    {
        sum = sum * (c - '0');
        if (sum > k)
        {
            ok = 1;
            break;
        }
    }
    if (s.find('0') != s.npos) // 如果有字符 0 必然不超过 k
        ok = 0;
    if (!ok) cout << "kawaii\n";
    else cout << "dame\n";
}
```


___


## T2

知识点：模拟


根据题意，如果一个文件被创建了，那么这个文件将会永远存在。所以我们创建一个变量来维护当前所有文件的长度总和。

在具体实现中可以把空格与数字字符的总长度分开。空格个数等于数字的个数在减去 $1$。

- 使用 `map<int, int> cnt` 维护有几个数字。（也可以用一个数组判断）


- 定义 `ans[N]` 数组记录每个数字最终的答案，即文件里有多少个字符。


- 定义数位拆分函数 `int f(int x)` 计算数字 $x$ 的位数。
- 定义变量 $sum$ 维护当前文件名的字符总个数。


当输入一个文件名 $x$，若 $x$ **第一次出现**（还没有这个文件名），则执行 $sum\leftarrow sum + f(x)$。


则 `ans[x] = sum + cnt.size() - 1`，即文件名字符总长度加上空格的个数。


```cpp
int n, m;
cin >> n >> m;
map<int, int> cnt;
int sum = 0;
for (int i = 1; i <= n; i++)
{
    int x;
    cin >> x;
    if (!cnt.count(x))
    {
        sum += f(x);
    }
    cnt[x] = 1;
    int sz = cnt.size();
    ans[x] = sum + sz - 1;
}
for (int i = 1; i <= m; i++) cout << ans[i] << " ";
```


___


## T3

知识点：差分，前缀和。


注意到一旦给一个数字加 $1$ 以后，则会改变它的奇偶性。因此我们只需要知道每个位置的数字是否被加过，而不是真的去做到区间修改，找到这个区间所有的奇数后给相应的加 $1$。


这启发我们使用差分算法，快速维护完所有的修改操作。

最终若一个位置需要加，且当前位置是奇数，则给该数字加 $1$ 即可。


时间复杂度：$O(n+m)$。


```cpp
cin >> n >> m;
for (int i = 1; i <= n; i++)
    cin >> a[i];
while (m--)
{
    int l, r;
    cin >> l >> r;
    diff[l] += 1;
    diff[r + 1] -= 1;
}
for (int i = 1; i <= n; i++) diff[i] += diff[i - 1];
long long res = 0;
for (int i = 1; i <= n; i++)
{
    if (diff[i] && a[i] % 2 == 1)
        a[i] += 1;
    res += a[i];
}
cout << res;
```


___


## T4

知识点：贪心，二分答案。

**暴力**

每次魔法可以保住 $k$ 块冰的质量不减少，自然是保住质量最小的 $k$ 块冰不减少。

因此可以使用小根堆动态维护前 $k$ 个最小的数字。

直到堆顶的值为 $0$ 即可。


**满分做法**



答案存在单调性，如果 $10$ 秒内没有冰块融化，显然更短的时间都不会用冰块融化。因此考虑二分答案 $s$。

将问题转变为判定性问题：如何判定 $s$ 秒以内不会有冰块融化。

每次操作可以防止 $k$ 块冰融化，相当于质量补充了 $k$，则 $s$ 秒一共可以补充 $k\times s$ 的质量。


其实可以不用仔细研究到底给哪些冰块去操作，而是考虑一块冰是否会在 $s$ 秒内融化，如果融化了需要补充多少质量？

因此可以求出所有冰块经过 $s$ 秒以后的情况，若 $a[i]-s\leq 0$ 则说明其融化了，则需要补充 $1-(a[i]-s)$ 的质量。


求出补充的总和 `sum` 与 $k\times s$ 比较即可判断是否可行。


时间复杂度：$O(n\log{v})$，其中 $v=10^{12}$。


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int N = 1e6 + 5;
int n, k, a[N]; 
bool check(int x)
{
    int sum = 0;
    for (int i = 1; i <= n; i++)
    {
        if (a[i] - x <= 0)
        {
            sum += 1 - (a[i] - x);
        }
    }
    return sum <= k * x;
}
signed main()
{
    cin >> n >> k;
    for (int i = 1; i <= n; i++) cin >> a[i];
    int l = 0, r = 1e12, ans = 0;
    while (l <= r)
    {
        int mid = l + r >> 1;
        if (check(mid)) ans = mid, l = mid + 1;
        else r = mid - 1;
    }
    cout << ans;
    return 0;
} 
```


___


## T5

知识点：最短路

由于边权有正数也有负数，因此无法使用 `dijkstra` 转化最长路为最短路。

- 使用 `spfa` 求最长路可以得到 $70$ 分。
- 但 `spfa` 最坏时间复杂度为 $O(nm)$ 无法通过。


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 2e5 + 5;
int n, m, h[N], dis[N];
vector<int> e[N];
bool vis[N];
void spfa(int s)
{
    for (int i = 1; i <= n; i++) dis[i] = -1e18;
    queue<int> q;
    q.push(s);
    vis[s] = 1;
    dis[s] = 0;
    while (q.size())
    {
        int u = q.front();
        q.pop();
        vis[u] = 0;
        for (auto v : e[u])
        {
            int w = 0;
            if (h[v] > h[u])
                w -= 2 * (h[v] - h[u]);
            else
                w += h[u] - h[v];
            if (dis[v] < dis[u] + w)
            {
                dis[v] = dis[u] + w;
                if (!vis[v])
                {
                    vis[v] = 1;
                    q.push(v);
                }
            }
        }
    }
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> h[i];
    while (m--)
    {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v), e[v].push_back(u);
    }
    spfa(1);
    int ans = -1e18;
    for (int i = 1; i <= n; i++) ans = max(ans, dis[i]);
    cout << ans;
    return 0;
}
```

___

本题的难点在于消除负数边权最终在正边权上使用 `dijkstra` 求 **最短路**。


**转化技巧**

首先最长路等价于边权全部取相反数以后的最短路。因此具体建图如下：


- 当 $h[u]>h[v]$，原本建立一条 $(u,v,h[u]-h[v])$ 的边。
    - 现在改为 $(u,v,h[v]-h[u])$。
- 当 $h[u]\leq v[h]$，原本建立一条 $(u,v,-2\times (h[v]-h[u]))$ 的边。
    - 现在改为 $(u,v,2\times (h[v]-h[u]))$ 的边。

给所有的边的边权都增加 $h[u]-h[v]$ 得到。


- 当 $h[u]>h[v]$，建立 $(u,v,0)$ 的边。
- 当 $h[u]\leq h[v]$，建立 $(u,v,h[v]-h[u])$ 的边。


这样操作后所有的边权都是非负的。接下来我们看一下所有边权都增加 $h[u]-h[v]$ 以后对最终答案的影响。


假设最终的最短路径为 $1\to a\to b\to c\to \ldots\to i$。


那么每条路径增加的边权总和为

$$
h[1]-h[a]+h[a]-h[b]+h[b]-h[c]+\ldots-h[i]
$$

因此操作后对答案的增加是 $h[1]-h[i]$。


因此实际到达点 $i$ 的最短路应该是 $dis[i]-(h[1]-h[i])$。

求出这个的最小值以后取相反数得到最大值，即最大快乐值。


```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 2e5 + 5;
int n, m, h[N], dis[N];
vector<pair<int, int>> e[N];
bool vis[N];
void dijkstra(int s)
{
    for (int i = 1; i <= n; i++) dis[i] = 1e18;
    dis[s] = 0;
    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
    q.push({0, s});
    while (q.size())
    {
        auto [d, u] = q.top();
        q.pop();
        if (vis[u]) continue;
        vis[u] = true;
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
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++) cin >> h[i];
    while (m--)
    {
        int u, v;
        cin >> u >> v;
        if (h[u] > h[v])
        {
            e[u].push_back({v, 0});
            e[v].push_back({u, h[u] - h[v]});
        }
        else
        {
            e[v].push_back({u, 0});
            e[u].push_back({v, h[v] - h[u]});
        }
    }
    dijkstra(1);
    int ans = 1e18;
    for (int i = 1; i <= n; i++) ans = min(ans, dis[i] - (h[1] - h[i]));
    cout << -ans; // 相反数得到最大值
    return 0;
}
```


___


## T6

知识点：博弈，思维，ST 表。

本题改编自 [CSP-S 2020] 策略游戏。提高组第二题。难度为绿题。

本题解中 $a[i]$ 等同于 $P[i]$，$b[i]$ 等同于 $T[i]$，$c[i][j]$ 等同于 $S[i][j]$。

定义 $A$ 为第一个人，$B$ 为第二个人。

**暴力**

首先用 $O(nm)$ 的时间在输入时构造出矩阵 $c[i][j]$。


$A$ 希望得分尽量大，$B$ 希望得分尽量小。


一旦 $A$ 在区间 $[l_1,r_1]$ 选择一个下标 $x$ 以后，$B$ 为了降低得分自然是在矩阵 $c[x][l_2\sim r_2]$ 找一个最小的数字出来。


因此暴力枚举即枚举 $[l_1,r_1]$ 的下标 $x$，然后求 $c[x][l_2\sim r_2]$ 的最小值。在所有的最小值中取最大的就是最终的结果。

理论时间复杂度为 $O(nmq)$ 实际可以通过前 $12$ 个测试点。


```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 1e3 + 5;
int n, m, q;
ll a[N], b[N], c[N][N];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m >> q;
    for (int i = 1; i <= n; i++) cin >> a[i];
    for (int i = 1; i <= m; i++) cin >> b[i];
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            c[i][j] = a[i] * b[j];
    while (q--)
    {
        int l1, r1, l2, r2;
        cin >> l1 >> r1 >> l2 >> r2;
        ll ans = -1e18;
        for (int i = l1; i <= r1; i++) 
        {
            ll mn = 1e18;
            for (int j = l2; j <= r2; j++)
            {
                mn = min(mn, c[i][j]);
            }
            // 所有最小的结果中取最大
            ans = max(ans, mn);
        }
        cout << ans << "\n";
    }
    return 0;
}
```


___


**满分优化**

首先可以思考特殊性质 $1$ 能干什么。

特殊性质 $1$ 告诉我们所有的数字都是正数，那么作为先选的人 $A$ 它必然是选择区间 $a[l_1\sim r_1]$ 中最大的数字记作 $mx$，$B$ 为了使结果尽量小，必然是选择 $b[l_2\sim r_2]$ 中最小的数字记作 $mn$。则最终答案即为 $mx\times mn$。


那么可以使用两个 ST 表，一个维护序列 $a$ 的区间最大值，一个维护序列 $b$ 的区间最小值。

通过 ST 表 $O(1)$ 查询求得区间最大值和最小值可以多得 $15$ 分。


___


思考负数带来的影响。

$A$ 选一个最大的数字。（无论正负）

- 若 $A$ 选了非负的数字，则 $B$ 用最小的数字的回应。
- 若 $A$ 选了负数，则 $B$ 用最大的数字回应。


___

$A$ 选一个最小的数字。（无论正负）


- 若 $A$ 选了非负的数字，则 $B$ 用最小的数字的回应。
- 若 $A$ 选了负数，则 $B$ 用最大的数字回应。


___


$A$ 选负数的最大值。（前提区间 $[l_1,r_1]$ 存在）

- $B$ 只能用最大的数字回应。


$A$ 选非负数的最小值。（前提区间 $[l_1,r_1]$ 存在）

- $B$ 只能用最小的数字回应。


因此需要 $6$ 个 ST 表。

- $A$ 的最大值，最小值，非负最小值，负数最大值。
- $B$ 的最大值，最小值。


在具体实现中：


- 例如维护非负最小值的 ST 表，若 $a[i]<0$，则初始化 $f[i][0]=10^{18}$。
- 同理一些细节的初始化，用于判断是否存在非负最小，以及负数的最大值用。


```cpp
#include <bits/stdc++.h>
#define inf 1e18
#define ll long long
using namespace std;
const int N = 1e5 + 5;
ll n, m, q;
ll a[N], b[N], Log2[N];
ll amx[N][17], amn[N][17], afmx[N][17], azmn[N][17]; // a的最大值，最小值，负数最大值，正数最小值
ll bmx[N][17], bmn[N][17];  
ll query_amx(int l, int r)
{
    int k = Log2[r - l + 1];
    return max(amx[l][k], amx[r - (1 << k) + 1][k]);
}
ll query_amn(int l, int r)
{
    int k = Log2[r - l + 1];
    return min(amn[l][k], amn[r - (1 << k) + 1][k]);
}
ll query_afmx(int l, int r)
{
    int k = Log2[r - l + 1];
    return max(afmx[l][k], afmx[r - (1 << k) + 1][k]);
}
ll query_azmn(int l, int r)
{
    int k = Log2[r - l + 1];
    return min(azmn[l][k], azmn[r - (1 << k) + 1][k]);
}
ll query_bmx(int l, int r)
{
    int k = Log2[r - l + 1];
    return max(bmx[l][k], bmx[r - (1 << k) + 1][k]);
}
ll query_bmn(int l, int r)
{
    int k = Log2[r - l + 1];
    return min(bmn[l][k], bmn[r - (1 << k) + 1][k]);
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin >> n >> m >> q;
    for (int i = 2; i <= 1e5; i++) Log2[i] = Log2[i >> 1] + 1; // 直接预处理到 1e5 避免两个序列长度不同的影响
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        amx[i][0] = amn[i][0] = a[i];
        if (a[i] < 0)
        {
            afmx[i][0] = a[i];
            azmn[i][0] = inf; // 最小正数设为无穷大，意为不存在，避免求最小值干扰。
        }
        else
        {
            afmx[i][0] = -inf; // 最大负数设为无穷小，意为不存在，避免求最大值干扰。
            azmn[i][0] = a[i];
        }
    }
    for (int j = 1; (1 << j) <= n; j++)
    {
        for (int i = 1; i + (1 << j) - 1 <= n; i++)
        {
            amx[i][j] = max(amx[i][j - 1], amx[i + (1 << (j - 1))][j - 1]);
            amn[i][j] = min(amn[i][j - 1], amn[i + (1 << (j - 1))][j - 1]);
            afmx[i][j] = max(afmx[i][j - 1], afmx[i + (1 << (j - 1))][j - 1]);
            azmn[i][j] = min(azmn[i][j - 1], azmn[i + (1 << (j - 1))][j - 1]);
        }
    }
    for (int i = 1; i <= m; i++)
    {
        cin >> b[i];
        bmx[i][0] = bmn[i][0] = b[i];
    }
    for (int j = 1; (1 << j) <= m; j++)
    {
        for (int i = 1; i + (1 << j) - 1 <= m; i++)
        {
            bmx[i][j] = max(bmx[i][j - 1], bmx[i + (1 << (j - 1))][j - 1]);
            bmn[i][j] = min(bmn[i][j - 1], bmn[i + (1 << (j - 1))][j - 1]);
        }
    }
    while (q--)
    {
        int l1, r1, l2, r2;
        cin >> l1 >> r1 >> l2 >> r2;
        ll bmax = query_bmx(l2, r2), bmin = query_bmn(l2, r2);
        ll amax = query_amx(l1, r1), amin = query_amn(l1, r1);
        ll afmax = query_afmx(l1, r1), azmin = query_azmn(l1, r1);
        ll ans = -inf;
        if (amax > 0) // A 的最大值大于 0，则 B 用最小的回应
        {
            ans = max(ans, amax * bmin);
        }
        else // A 的最大值小于等于 0，则 B 用最大的回应
        {
            ans = max(ans, amax * bmax);
        }
        if (amin < 0) // A 的最小值小于 0，则 B 用最大的回应
        {
            ans = max(ans, amin * bmax);
        }
        else // A 的最小值大于等于 0，则 B 用最小的回应
        {
            ans = max(ans, amin * bmin);
        }
        // A 存在最大的负数，此时 B 用最大值回应。例如 -8 * 5 优于 -8 * 1。-8 * -1 优于 -8 * -3
        if (afmax != -inf)
            ans = max(ans, afmax * bmax);
        // A 存在最小的正数，此时 B 用最小值回应。例如 8 * 1 优于 8 * 3。8 * -5 优于 8 * -1
        if (azmin != inf)
            ans = max(ans, azmin * bmin);
        cout << ans << "\n";
    }
    return 0;
}
```








