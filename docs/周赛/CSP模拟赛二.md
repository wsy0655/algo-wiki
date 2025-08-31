
## T1


知识点：数学，条件判断。

不难发现 $x=\lfloor \frac{p}{q}\rfloor$，$y=\lceil \frac{p}{q}\rceil$。

分别使用 `floor` 和 `ceil` 函数即可求出 $x,y$。

当满足 $\frac{p}{q}-x\leq y-\frac{p}{q}$ 时，输出 $x$。否则输出 $y$。
___

## T2


知识点：数学，循环。

根据 $\sqrt{a}\cdot \sqrt{b}=\sqrt{ab}$，可以得到：

设 $x=k^2\cdot y$，则 $\sqrt{x}=\sqrt{k^2\cdot y}=\sqrt{k^2}\cdot \sqrt{y}=k\cdot \sqrt{y}$。

说白了 $k^2$ 是 $x$ 最大的完全平方因子，因此直接枚举 $x$ 的完全平方因子求最大的即可。

时间复杂度：$O(\sqrt{x})$

```cpp
for (long long k = 1; k * k <= x; k++)
{
    if (x % (k % k) == 0)
    {
        mx = k;
    }
}
```

---

## T3

知识点：思维，分类讨论。

首先要注意到答案不超过 $3$。其次整个序列可以看作是分为了若干段，每一段内的颜色都是相同的。

- 两个格子颜色相同，答案为 $1$。
- 颜色不同：
    - 若两个格子相邻，则答案为 $1$。
    - 若两个格子有一个格子是当前段的左右端点，则答案为 $2$。
    - 否则为 $3$。

注意：环形序列注意相邻的特殊情况。字符串下标从 $0$ 开始。

时间复杂度：$O(n+q)$。


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 5e5 + 5;
int n, q;
bool vis[N];
string s;
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> q >> s;
    for (int i = 1; i < n; i++)
    {
        if (s[i] != s[i - 1])
        {
            vis[i] = 1; // i 是左边界
            vis[i - 1] = 1; // i - 1 是右边界
        }
    }
    if (s[0] != s[n - 1])
    {
        vis[0] = 1;
        vis[n - 1] = 1;
    }
    while (q--)
    {
        int x, y;
        cin >> x >> y;
        x--, y--;
        if (x > y) swap(x, y);
        if (s[x] == s[y])
        {
            cout << "1\n";
        }
        else
        {
            if ((y - x == 1) || (x == 0 && y == n - 1)) // 位置相邻
            {
                cout << "1\n";
            }
            else // 位置不相邻且颜色不同，答案要么是 2 要么是 3
            {
                if (vis[x] || vis[y])
                {
                    cout << "2\n";
                }
                else
                {
                    cout << "3\n";
                }
            }
        }
    }
    return 0;
}
```

___

## T4


知识点：前缀和，分治。


最后一次合成可以看作是将整个序列分为俩个部分 $L,R$，满足：

- $0\leq sum_L-sum_R\leq 1$。

注意到 $a_i\geq 1$，因此这样的合并位置是 **唯一的**。

因此可以直接暴力枚举（或二分）求出合并位置 $mid$。

若该位置存在，则整个序列分为两个部分 $1\sim mid$ 和 $mid+1\sim n$，我们要做的就是继续判断左右两部分是否可以合并出来，那么就可以分治下去求解该问题了。

时间复杂度：$O(n\log n)$

```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
constexpr int N = 5e5 + 5;
ll n, a[N], s[N];
bool dfs(int l, int r)
{
    if (l == r) 
        return true;
    int mid = -1;
    for (int i = l; i <= r; i++)
    {
        ll L = s[i] - s[l - 1];
        ll R = s[r] - s[i];
        if (L - R >= 0 && L - R <= 1)
            mid = i;
    }
    if (mid == -1) 
        return false;
    else 
        return dfs(l, mid) && dfs(mid + 1, r);
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i];
        s[i] = s[i - 1] + a[i];
    }
    if (dfs(1, n)) 
        cout << "Yes";
    else
        cout << "No";
    return 0;
}
```


___



## T5

知识点：贪心

在不考虑茶的情况下，自然是选择最大的 $m$ 块蛋糕分配下去。让我们尝试将部分茶和蛋糕进行匹配。使得最终选择正确的前 $n$ 大。


首先将蛋糕按照美味度从大到小排序（优先给美味度大的蛋糕分配茶），贪心的想法就是给蛋糕最大的美味度的茶（且需要保证品牌相同）

同一品牌的茶可能有很多，且用掉一个茶就不能再给其它蛋糕使用，因此这启发我们针对每个品牌的茶，维护一个大根堆。


```cpp
priority_queue<int> q[200005];
for (int i = 1; i <= m; i++)
    cin >> b[i].first; // 品牌
for (int i = 1; i <= m; i++)
    cin >> b[i].second; // 美味度
for (int i = 1; i <= m; i++)
    q[b[i].first].push(b[i].second); 
```

那么接下来枚举所有的蛋糕。

- 枚举蛋糕的编号 $i$
    - 记当前蛋糕的品牌和美味度分别为 $[x,y]$。
    - 若 `if (!q[x].empty())` ，则说明当前品牌 `x` 对应的茶存在。
    - 此时大根堆堆顶弹出美味度最大的茶，并加入当前蛋糕与蛋糕匹配。
    - 即可以令 $y\leftarrow y+q[x].top()$ 。
  
操作完以后，部分蛋糕得到了一定的补强，因此重新按照美味度从大到小排序，求前 $n$ 大的和即可。

时间复杂度：$O((n+m)\log n)$。

___


## T6

知识点：动态规划，树状数组。


**子任务1**

使用二进制枚举或 DFS 枚举所有子集，判断每个子集是否可以满足任意相邻的两座塔 $(i,i+1)$ 满足：

$$
a_{i+1}\leq b_i\leq a_{i+1}+L
$$

即可，时间复杂度：$O(2^n\cdot n)$。


___


**子任务2**

类似于 LIS 问题，定义 $f_i$ 代表以 $i$ 结尾的塔满足要求的数量。

转移：枚举上一个塔 $j$，满足 $a_j\leq b_i\leq a_j+L$，则 $f_i=f_j+1$。

$$
f_i=\sum\limits_{j=1}^{i-1} f_j\cdot [a_j\leq b_i\leq a_j+L]
$$

> $[x]$ 为艾佛森括号，当括号内条件成立表达式为 $1$，否则为 $0$。

时间复杂度：$O(n^2)$。


___



**子任务3**

由于 $L=0$，因此转移变为了

$$
f_i=\sum\limits_{j=1}^{i-1} f_j\cdot [a_j=b_i]
$$

因此可以使用一个值域数组 `cnt`，维护所有 $b_i$ 的 DP 值之和，那么转移就可以 $O(1)$ 了。

> 这种技巧在 DP 优化那节课讲过。例如：[蓝桥杯 2023 省 B] 接龙数列



___


**子任务4**


根据子任务 $3$ 的提示，我们就是要将满足：$a_i\sim a_i+L$ 一段连续值域的 DP 值之和快速查询出来就可以加速转移了。

可以使用树状数组维护 $b_i$ 值域上 DP 值，使用树状数组快查询值域区间和即可。


时间复杂度：$O(n\log{V})$。


```cpp
for (int i = 1; i <= n; i++)
{
    f[i] = 1 + bit.query(a[i], a[i] + L);
    ans += f[i];
    bit.add(b[i], f[i]);
}
cout << ans;
```


___


## T7

知识点：二分，并查集。


**子任务1**

只有 $1$ 列，相当于是一个序列问题。

给定一个长度为 $n$ 的序列，第 $i$ 个陆地在位置 $x_i$，问至少几秒，所有格子连通。

实现方式：

- 首先按照 $x_i$ 从小到大排序所有陆地。
- 陆地 $i$ 与陆地 $i+1$ 的连通时间为 $x_{i+1}-x_{i}-1$。
- 求所有相邻陆地连通时间的最大值即可。


___


**子任务2**

只有 $2$ 行，因此不需要考虑每一列第 $1$ 行与第 $2$ 行的连通时间。（不需要时间即可连通）

因此只需要考虑相邻两列的连通时间，因此答案要么是 $1$ 要么是 $2$。

如果所有陆地都在同一行那么答案就是 $1$，否则就是 $2$。

> 注意：若 $n$ 个陆地分布的列不相邻，则答案为 $-1$。


___


**子任务$3\sim 7$**


二分答案 $t$，将问题转变为判定性问题，即判定在 $t$ 天内能否实现所有陆地连通，连通性采用并查集维护。


根据子任务 $1$ 和 $2$ 的启发，我们可以先处理每一列的陆地的连通性，在处理相邻两列的陆地是否可以连通到一起。

___

**每一列的陆地**

按照子任务 $1$ 的做法，将每一列的陆地按照 $x$ 升序排序，遍历该列的所有陆地，若满足 $x_{i+1}-x_i\le t+1$，则使用并查集将这两个陆地连通起来。


在具体实现中：


- 使用 `vector<int> a[N]` 存储每一列所有陆地行的坐标和陆地编号。

```cpp
bool cmp(int a, int b)
{
    return x[a] < x[b]; // 按照行的坐标排序不是编号
}
for (int i = 1; i <= n; i++)
{
    cin >> x[i] >> y[i];
    a[y[i]].push_back(i);
}
for (int i = 1; i <= w; i++) // 对每一列的陆地排序
{
    sort(a[i].begin(), a[i].end(), cmp)
}
```
___


接下来实现一个 `check(t)` 的检查函数，判断 $t$ 天内是否可以都连通。


```cpp
bool check(int t)
{
    // 注意这里初始化并查集
    for (int i = 1; i <= w; i++)
    {
        for (int j = 1; j < a[i].size(); j++)
        {
            int p = x[a[i][j - 1]], q = x[a[i][j]];
            if (p + t + 1 >= q)
            {
                // 合并的是陆地编号不是行坐标
                merge(a[i][j - 1], a[i][j]);
            }
        }
    }
}
```


**相邻两列的处理**

暴力做法是枚举第 $i$ 列的所有陆地 $p$，在枚举第 $i+1$ 列的陆地 $q$，若满足 $p+t\leq q$，则合并陆地 $p,q$ 对应的编号。

这样做时间复杂度显然太高。

我们可以将 $i,i+1$ 两列的陆地全部存储到一个数组中，然后按照行编号从小到大排序，这样就只需要处理相邻两个陆地的问题。


```cpp
for (int i = 1; i < w; i++)
{
    vector<int> temp;
    for (auto x : a[i]) 
        temp.push_back(x);
    for (auto x : a[i + 1]) 
        temp.push_back(x);
    sort(temp.begin(), temp.end(),cmp);
    for (int j = 1; j < temp.size(); j++)
    {
        int p = x[temp[j - 1]], q = x[temp[j]];
        if (p + t >= q)
        {
            merge(temp[j - 1], temp[j]);
        }
    }
}
```


___

最后判断所有陆地是否已经连通即可。（相当于所有陆地的祖先都相同，统计祖先个数判断是否等于 $1$ 即可）


___

最终整体时间复杂度为 $O(h\cdot w\cdot \log^2(h\cdot w))$，忽略并查集的操作时间复杂度。


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 2e5 + 5;
int h, w, n, x[N], y[N], p[N];
vector<int> a[N];
bool cmp(int a, int b)
{
    return x[a] < x[b]; // 按照行的位置排序
}
int find(int x)
{
    return x == p[x] ? x : p[x] = find(p[x]);
}
void merge(int x, int y)
{
    x = find(x), y = find(y);
    if (x == y) return;
    p[x] = y;
}
bool check(int t)
{
    for (int i = 1; i <= n; i++) p[i] = i;
    for (int i = 1; i <= w; i++)
    {
        for (int j = 1; j < a[i].size(); j++)
        {
            int p = x[a[i][j - 1]], q = x[a[i][j]];
            if (p + t + 1 >= q)
            {
                merge(a[i][j - 1], a[i][j]);
            }
        }
    }
    for (int i = 1; i < w; i++)
    {
        vector<int> temp;
        for (auto x : a[i]) temp.push_back(x);
        for (auto x : a[i + 1]) temp.push_back(x);
        sort(temp.begin(), temp.end(), cmp);
        for (int j = 1; j < temp.size(); j++)
        {
            int p = x[temp[j - 1]], q = x[temp[j]];
            if (p + t >= q)
            {
                merge(temp[j - 1], temp[j]);
            }
        }
    }
    int cnt = 0;
    for (int i = 1; i <= n; i++) if (find(i) == i) cnt++;
    return cnt == 1;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> h >> w >> n;
    for (int i = 1; i <= n; i++)
    {
        cin >> x[i] >> y[i];
        a[y[i]].push_back(i);
    }
    for (int i = 1; i <= w; i++) 
    {
        sort(a[i].begin(), a[i].end(), cmp);
    }
    int l = 0, r = h, ans = -1;
    while (l <= r)
    {
        int mid = l + r >> 1;
        if (check(mid))
            ans = mid, r = mid - 1;
        else
            l = mid + 1;
    }
    cout << ans;
    return 0;
}
```