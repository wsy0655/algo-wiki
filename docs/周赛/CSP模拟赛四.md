## T1

知识点：条件判断。

<details>


<summary>代码</summary>

```cpp
if (s == 'N')
{
    if (t <= 3) cout << 0;
    else if (t == 4) cout << 3;
    else if (t == 5) cout << 5;
    else cout << 9;
}
else
{
    if (t <= 3) cout << 0;
    else if (t == 4) cout << 0;
    else if (t == 5) cout << 1;
    else cout << 5;
}
```

</details>



___


## T2


知识点：循环。


注意跨越 $0$ 层时要多计算一次即可，具体可以看代码。


<details>

<summary>代码</summary>

```cpp
int a, b, n, m;
cin >> a >> b >> n >> m;
for (int i = 1; i <= m; i++)
{
    int h;
    cin >> h;
    if (h > 0)
    {
        if (n > 0) n += h;
        else 
        {
            if (n + h >= 0) n += h + 1; // 跨越 0 层多加 1 次
            else n += h;
        }
    }
    else
    {
        if (n < 0) n += h;
        else 
        {
            if (n + h <= 0) n += h - 1;  // 跨越 0 层多减 1 次
            else n += h;
        }
    }
}
cout << n;
```

</details>

___


## T3

知识点：枚举，字符串。


记 `pre` 为所有字符串的最长公共前缀的长度，记 `suf` 为所有字符串的最长公共后缀的长度。

那么答案为 $\min(pre + suf, m)$。

数据范围较小，可以直接枚举所有字符串的最长公共前缀和最长公共后缀的长度分别求解即可。

> 使用 `substr()` 函数可以方便地求出前缀与后缀。

<details>

<summary>代码</summary>

```cpp
for (int i = 1; i <= n; i++) cin >> s[i];
int pre = 0;
for (int len = 1; len <= m; len++) // 枚举前缀长度
{
    string tag = s[1].substr(0, len); // 切割出第一个字符串的前缀
    bool ok = 1;
    for (int i = 2; i <= n; i++) // 和剩余所有字符串的前缀比较一遍
    {
        if (s[i].substr(0, len) != tag)
        {
            ok = 0;
            break;
        }
    }
    if (ok) pre = len;
}
int suf = 0;
for (int len = 1; len <= m; len++) // 枚举后缀长度
{
    string tag = s[1].substr(m - len); // 切割出第一个字符串的后缀
    bool ok = 1;
    for (int i = 2; i <= n; i++) // 和剩余所有字符串的后缀比较一遍
    {
        if (s[i].substr(m - len) != tag)
        {
            ok = 0;
            break;
        }
    }
    if (ok) suf = len;
}
if (pre + suf >= m) cout << m;
else cout << pre + suf;
```

</details>

___


## T4

知识点：枚举


**问题回顾**

我们要计算有多少个六元组 $(i,j,k,x,y,z)$ 满足：

$$
A_i + B_j + C_k + D_x + E_y + F_z = 0
$$

其中每个下标范围是 $1 \le i,j,k,x,y,z \le n$。

---


**暴力做法不可行**

如果直接枚举所有 $i,j,k,x,y,z$，复杂度是 $O(n^6)$。

题目里 $n \leq 100$，那么最多 $100^6 = 10^{12}$ 次运算，肯定超时。


---

 **分组求和**

把式子分成两部分：

$$
(A_i + B_j + C_k) + (D_x + E_y + F_z) = 0
$$

于是我们只要：

* 枚举所有 $A_i + B_j + C_k$，存进哈希表，统计出现次数。
* 再枚举所有 $D_x + E_y + F_z$，查找相反数的组合个数即可。

---

**第一部分：存储三数和**

```cpp
unordered_map<int, int> mp;
for (int i = 1; i <= n; i++) 
{
    for (int j = 1; j <= n; j++) 
    {
        for (int k = 1; k <= n; k++) 
        {
            mp[a[i] + b[j] + c[k]]++;
        }
    }
}
```

* 枚举所有 $(i,j,k)$，求和 $s = A_i + B_j + C_k$。
* 在哈希表 `mp` 里记录每个和的出现次数。

复杂度：$O(n^3)$，最多 $100^3 = 10^6$，可行。

---

**第二部分：查找相反数**

```cpp
long long ans = 0;
for (int x = 1; x <= n; x++) 
    for (int y = 1; y <= n; y++) 
        for (int z = 1; z <= n; z++) 
            ans += mp[-(d[x] + e[y] + f[z])];
```

* 枚举所有 $(x,y,z)$，计算和 $t = D_x + E_y + F_z$。
* 想要满足总和为 0，必须有：

  $$
  A_i + B_j + C_k = -t
  $$

* 所以查找 `mp[-t]`，加到答案里。

复杂度同样是 $O(n^3)$。

---

**总体复杂度**

* 存哈希表部分：$O(n^3)$
* 查找部分：$O(n^3)$
* 总复杂度：$O(n^3)$，即最多 $2 \times 10^6$，完全能跑得动。





___



## T5

知识点：BFS。


本题可以抽象为一张带权图：

* **正常走**：代价为 $0$，只能在道路 `.` 上进行。
* **跳跃**：代价为 $1$，当前位置无要求，只要目标格子在地图内即可。

目标是 **最小化跳跃次数**，而不是最小化步数。

**拆点建模**

为了区分“通过走路到达某格子”和“通过跳跃到达某格子”，我们为每个格子引入两种状态：

* $dis_{i,j,0}$：到达 $(i,j)$，最后一步是**走**的最小跳跃数。
* $dis_{i,j,1}$：到达 $(i,j)$，最后一步是**跳**的最小跳跃数。

初始化：

* $dis_{1,1,0}=0$，其余状态均为 $-1$，表示未访问。

**0-1 BFS 流程**

使用双端队列维护状态：

- 取出队头 $(x,y,t)$。
- **尝试正常走**：
     * 前提：当前位置和目标位置均为道路 `.`。
     * 若 $dis_{nx,ny,0}=-1$，则更新 $dis_{nx,ny,0}=dis_{x,y,t}$，并加入队首（代价 $0$）。
- **尝试跳跃**：
     * 前提：目标在地图范围内。
     * 若 $dis_{nx,ny,1}=-1$，则更新 $dis_{nx,ny,1}=dis_{x,y,t}+1$，并加入队尾（代价 $1$）。

最终答案为：

$$
\min\{\,dis_{n,m,0},\; dis_{n,m,1}\,\}
$$

若均为 $-1$，则输出 $-1$。

由于每个格子最多扩展一次，复杂度为 $O(nm)$，可满足 $n,m \leq 1000$ 的限制。





<details>

<summary>代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 1e3 + 5;
constexpr int dx[8] = {0, 1, 0, -1, 2, -2, 0, 0}, dy[8] = {1, 0, -1, 0, 0, 0, 2, -2};
int n, m, dis[N][N][2];
char a[N][N];
deque<array<int, 3>> dq;
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> a[i][j];
    memset(dis, -1, sizeof(dis));
    dq.push_back({1, 1, 0});
    dis[1][1][0] = 0;
    while (!dq.empty())
    {
        auto [x, y, t] = dq.front();
        dq.pop_front();
        for (int i = 0; i < 4; i++)
        {
            int nx = x + dx[i], ny = y + dy[i];
            if (nx < 1 || nx > n || ny < 1 || ny > m || a[nx][ny] == '#' || a[x][y] == '#')
                continue;
            if (dis[nx][ny][0] == -1)
            {
                dis[nx][ny][0] = dis[x][y][t] + 0;
                dq.push_front({nx, ny, 0});
            }
        }
        for (int i = 4; i < 8; i++)
        {
            int nx = x + dx[i], ny = y + dy[i];
            if (nx < 1 || nx > n || ny < 1 || ny > m)
                continue;
            if (dis[nx][ny][1] == -1)
            {
                dis[nx][ny][1] = dis[x][y][t] + 1;
                dq.push_back({nx, ny, 1});
            }
        }
    }
    int ans = 1e9;
    if (dis[n][m][0] != -1) ans = dis[n][m][0];
    if (dis[n][m][1] != -1) ans = min(ans, dis[n][m][1]);
    if (ans == 1e9) ans = -1;
    cout << ans;
    return 0;
}
```

</details>



## T6


知识点：构造，思维，图论。


**思路说明**

题目要求构造一个连通无向图，使得**每个点的所有邻居编号之和相同**。

一个直观办法是从**完全图**出发：完全图中，每个点的邻居和为

$$
\sum_{k=1}^n k - i = \tfrac{n(n+1)}{2} - i,
$$

这显然依赖于点的编号，不满足要求。我们需要通过**有规律地删除一些边**，让每个点的邻居和“补齐一致”。

---

**奇数 $n$ 的构造**

当 $n$ 为奇数时，可以保留编号正中的点（即 $\frac{n+1}{2}$ 号点）的全部边不动。
其余的点可以两两配对：$(i, n+1-i)$。

* 在完全图里删去这对点之间的边。
* 这样，每对对称点都少掉了相同的邻居（即 $n+1-i$ 对于 $i$，以及 $i$ 对于 $n+1-i$），因此它们的邻居和同时下降到同一个数。
* 中间那个点没少边，它的邻居和也正好与大家相同。

因此所有点的邻居和一致。

---

**偶数 $n$ 的构造**

当 $n$ 为偶数时，可以把所有点完全两两配对：$(i, n+1-i)$。

* 从完全图中删去这 $n/2$ 条边。
* 对每个点来说，删掉的邻居恰好是它的对称点（编号和 $n+1$）。
* 所以每个点的邻居和都变为

$$
\frac{n(n+1)}{2} - (n+1),
$$

与具体编号无关。

---


无论奇数还是偶数，删去的只是**匹配边**（点对之间的单条边），并不会把整个图分割开。因为原本是完全图，删去这些边后依然保持连通。

只需枚举 $O(n)$ 对点，删边并输出即可，整体复杂度 $O(n^2)$（因为输出边本身需要这么多）。


<details>

<summary>代码</summary>

```cpp
int sum = n * (n - 1) / 2 - n / 2;
cout << sum << "\n";
if (n % 2 == 0)
{
    for (int i = 1; i <= n; i++)
    {
        for (int j = i + 1; j <= n; j++)
        {
            if (i + j == n + 1) continue;
            cout << i << " " << j << "\n";
        }
    }
}
else
{
    for (int i = 1; i <= n; i++)
    {
        for (int j = i + 1; j <= n; j++)
        {
            if (i + j == n) continue;
            cout << i << " " << j << "\n";
        }
    } 
}
```

</details>

___


## T7

知识点：动态规划，状态压缩，BFS。



题目要求我们在给定字符串 $s,t$ 的基础上，通过**插入字符**（可以是任意大写字母或小写字母），构造一个公共超序列，使得任意两个相同字符之间的距离 $\ge k$，并且希望得到的超序列尽可能短。

可以先分析几个子任务：

---

**情况一：$s=t$**

如果 $s$ 与 $t$ 完全相同，只需要考虑单个字符串 $s$。

* 我们从左到右扫描 $s$，维护每个字符上一次出现的位置。
* 当两个相同字符之间的间隔 $<k$ 时，必须插入若干 **占位符** 字符（比如 `#`），贪心地在后一个字符之前插入即可。
    * 这么做的原因是：越靠后插入，对后续字符的影响越大。
* 最后得到满足限制的最短超序列。

这种做法复杂度为 $O(n^2)$（字符串 `insert` 是 $O(n)$），在数据范围内可接受。


<details>

<summary>代码</summary>

```cpp
if (s == t) // 子任务 1, 2, 3
{
    map<char, int> last;
    int ans = n;
    for (int i = 0; i < s.size(); i++)
    {
        if (last.count(s[i]))
        {
            if (i - last[s[i]] - 1 < k && s[i] != '#')
            {
                string t(k - (i - last[s[i]] - 1), '#');
                ans += k - (i - last[s[i]] - 1);
                s = s.insert(i, t);
            }
        }
        last[s[i]] = i;
    }
    cout << ans;
    return 0;
}
```

</details>


---

**情况二：$k=0$**

这时没有任何限制，问题就退化为**求两个字符串的最短公共超序列**。

经典结论：

$$
\text{最短公共超序列长度} = n+m - \text{LCS}(s,t),
$$

其中 $\text{LCS}(s,t)$ 表示 $s,t$ 的最长公共子序列长度。

因此我们只需计算 LCS 即可，复杂度 $O(nm)$。

<details>

<summary>代码</summary>

```cpp
s = " " + s, t = " " + t;
vector<vector<int>> f(n + 1, vector<int>(m + 1));
for (int i = 1; i <= n; i++)
{
    for (int j = 1; j <= m; j++)
    {
        if (s[i] == t[j])
        {
            f[i][j] = f[i - 1][j - 1] + 1;
        }
        else
        {
            f[i][j] = max(f[i - 1][j], f[i][j - 1]);
        }
    }
}
cout << n + m - f[n][m];
```

</details>


---

**情况三：$k=1$**

这里的关键是：我们不能再简单地依赖 LCS，因为**相同字符不能相邻**，有时必须插入额外字符来“隔开”。

因此需要把“上一个插入的字符”纳入状态：

* 定义 $dis_{i,j,c}$ 表示处理到 $s$ 的前 $i$ 个字符、$t$ 的前 $j$ 个字符，且最后一个插入的字符是 $c$ 时的最小超序列长度。
* 其中 $c$ 可以是 $52$ 种字母中的一种，或一个特殊状态“未插入”。
* 初始：$dis_{0,0,52}=0$。
* 转移：枚举下一个要插入的字符 $c$，如果 $c\neq$ 上一个字符，则合法；同时尽可能匹配 $s[i],t[j]$。

这个状态空间大小是 $O(nm\cdot 53)$，用 BFS 可以求解。


<details>

<summary>代码</summary>

```cpp
auto get = [&](char c) -> int
{
    if (isupper(c)) return c - 'A';
    else return c - 'a' + 26;
};

queue<array<int, 3>> q;
q.push({0, 0, 52});
vec<vec<vec<int>>> dis(n + 1, vec<vec<int>>(m + 1, vec<int>(53, 1e9)));
dis[0][0][52] = 0;

while (!q.empty())
{
    auto [i, j, last] = q.front();
    q.pop();
    for (int c = 0; c < 52; c++)
    {
        if (c == last) continue; // 和上一个相同则跳过
        int ni = i, nj = j;
        if (i < n && get(s[i]) == c) // 是否匹配 s[i]
            ni++;
        if (j < m && get(t[j]) == c) // 是否匹配 t[j]
            nj++;
        if (dis[ni][nj][c] == 1e9)
        {
            dis[ni][nj][c] = dis[i][j][last] + 1;
            q.push({ni, nj, c});
        }
    }
}
cout << *min_element(dis[n][m].begin(), dis[n][m].end());
```

</details>


---

**情况四：$k=2,3$**

如果继续沿用 $k=1$ 的方法，就需要记录**最近 $k$ 个字符**，也就是 $O(52^k)$ 的状态量，显然无法承受。

但注意：我们其实**不关心插入的字符具体是哪个字母**，而只关心它与 $s_i,t_j$ 是否冲突。

因此可以将“插入的字符”分类为 $4$ 种：

1. 插入了 $s_i$
2. 插入了 $t_j$
3. 插入了 $s_i=t_j$（两者相等的情况）
4. 插入了无关的字符

于是只需记录最近 $k$ 个字符对应的“类别”，状态数为 $4^k$。

* 当 $k\le 3$ 时，$4^k$ 最多 $64$，完全可行。
* 我们用一个压缩整数 `mask` 存储最近 $k$ 个字符类别（四进制表示）。

转移时：

* 判断能否在当前位置插入某个类别（避免与前 $k$ 个重复）。
* 更新 $dis_{i,j,mask}$。

这样即可在 $O(nm \cdot 4^k)$ 的复杂度内完成。


<details>

<summary>代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
using pii = pair<int, int>;
template <typename T>
using vec = vector<T>;
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n, m, k;
    cin >> n >> m >> k;
    string s, t;
    cin >> s >> t;

    auto check = [&](int i, int j, int mask) -> bool 
    {
        if (mask % 4 == 3) return true;
        char c1 = (mask % 4 == 0 ? s[i] : t[j]);
        int li = i, lj = j;
        for (int x = 0; x < k; x++)
        {
            mask /= 4; // 对四进制状态 mask 数位拆分
            if (mask % 4 == 3) continue; // 跳过无关字符
            char c2 = (mask % 4 == 0 ? s[li - 1] : t[lj - 1]);
            if (c1 == c2) return false;
            if (mask % 4 == 0) li--;
            else if (mask % 4 == 1) lj--;
            else li--, lj--;
        }
        return true;
    };

    
    queue<array<int, 3>> q;
    vec<vec<vec<int>>> dis(n + 1, vec<vec<int>>(m + 1, vec<int>(64, 1e9)));
    dis[0][0][63] = 0;
    q.push({0, 0, 63});

    auto update = [&](int i, int j, int mask, int ni, int nj, int nmask) -> void
    {
        if (ni > n || nj > m || dis[ni][nj][nmask] != 1e9) return ;
        dis[ni][nj][nmask] = dis[i][j][mask] + 1;
        q.push({ni, nj, nmask});
    };


    while (!q.empty())
    {
        auto [i, j, mask] = q.front();
        q.pop();
        if (s[i] == t[j])
        {
            int nmask = mask * 4 + 2; // s[i] == t[j]，状态 2
            if (check(i, j, nmask)) 
            {
                update(i, j, mask, i + 1, j + 1, mask % 16 * 4 + 2);
            }
            update(i, j, mask, i, j, mask % 16 * 4 + 3); // 插入一个无关字符
        }
        else
        {
            int nmask = mask * 4;
            if (check(i, j, nmask)) 
            {
                update(i, j, mask, i + 1, j, mask % 16 * 4 + 0); // 插入 s[i]
            }
            nmask = mask * 4 + 1;
            if (check(i, j, nmask))
            {
                update(i, j, mask, i, j + 1, mask % 16 * 4 + 1); // 插入 t[j]
            }
            update(i, j, mask, i, j, mask % 16 * 4 + 3); // 插入一个无关字符
        }
    }
    cout << *min_element(dis[n][m].begin(), dis[n][m].end());
    return 0;
}
```

</details>