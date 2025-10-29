## T1 压缩文章（str）

**知识点：字符串处理**

首先使用 `getline()` 函数将带空格的字符串一次性读入。

```cpp
string s;
getline(cin, s);
```

接下来通过**单次扫描字符串**的方式来实现：

1. **遍历输入的字符串**；
2. 用一个临时字符串 `t` 保存当前正在读取的单词；
3. 当遇到空格 `' '` 或句号 `'.'` 时，说明一个单词结束：
      * 若单词长度 $\leq 2$，直接输出；
      * 若长度 $>2$，按规则压缩输出；
      * 然后输出当前的空格或句号；
      * 清空 `t`，继续处理后续字符；
4. 遍历结束后，若最后还有未输出的单词（即 `t` 非空），再单独处理一次。

* **复杂度分析**
    * 每个字符只扫描一次，压缩操作是常数时间；
    * 总复杂度：**$O(|s|)$**；
    * 空间复杂度：**$O(|s|)$**。


<details>

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    freopen("str.in", "r", stdin);
    freopen("str.out", "w", stdout);
    ios::sync_with_stdio(false), cin.tie(0);
    string s;
    getline(cin, s);
    string t;
    for (int i = 0; i < s.size(); i++)
    {
        if (s[i] == ' ' || s[i] == '.')
        {   
            if (t.size() <= 2)
            {
                cout << t;
            }
            else
            {
                cout << t[0] << t.size() - 2 << t[t.size() - 1];
            }
            cout << s[i];
            t = "";
            continue;
        }
        else
        {
            t += s[i];
        }
    }
    if (t.size() <= 2)
    {
        cout << t;
    }
    else
    {
        cout << t[0] << t.size() - 2 << t[t.size() - 1];
    }
    return 0;
}
```

</details>


___



## T2 序列（seq）

**知识点：思维，模拟**

---



在不断进行“将前 $b$ 项放到末尾”的操作时，序列最终会进入一个**循环状态**：移动一定次数后，序列会重新回到最初的样子。

不难发现：

* 每次操作相当于对序列进行一次**循环左移**；
* 移动次数超过某个周期后，结果将重复。

设 $t$ 为序列重新回到初始状态所需的最小移动次数，则有：

* 若 $a$ 是 $n$ 的因子，则移动 $n/a$ 次即可回到初始；
* 若 $a$ 不是 $n$ 的因子，则移动 $n$ 次后必定回到初始状态。

因此，我们只需取有效移动次数：$a\gets a\bmod t$。

此时 $a \le n$，只需暴力模拟移动 $a$ 次即可求出最终结果。

总复杂度为 $O(n^2)$。


<details>

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
    freopen("seq.in", "r", stdin);
    freopen("seq.out", "w", stdout);
    ios::sync_with_stdio(false), cin.tie(0);
    i64 n, a, b;
    cin >> n >> a >> b;
    int t = (n % a == 0 ? n / a : n);
    a %= t;
    vector<int> seq(n); 
    iota(seq.begin(), seq.end(), 1); // 初始化序列为 1,2,3,4...n
    while (a--)
    {
        vector<int> nseq;
        for (int i = b; i < n; i++)
            nseq.push_back(seq[i]);
        for (int i = 0; i < b; i++)
            nseq.push_back(seq[i]);
        seq = nseq;
    }
    for (int x : seq) cout << x << " ";
    return 0;
}
```

</details>

___




## T3 最小公倍数（lcm）

**知识点：数论**

---

**前 20% 数据**

对于较小的数据范围（$1 \le n,m \le 5$），可以直接使用 **DFS** 暴力枚举所有 $m$ 个数字的取值，计算其最小公倍数并判断是否等于 $n$。

每次 DFS 层代表当前选择第 $i$ 个数字，递归到底后判断 LCM 是否等于 $n$ 即可。

时间复杂度约为 $O(n^m \log n)$。


<details>

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
int n, m, ans;
int lcm(int a, int b) 
{
    return a / __gcd(a, b) * b;
}
void dfs(int dep, int cur) 
{
    if (dep == m + 1) // 枚举完 m 个数
    { 
        if (cur == n) ans++; // 仅统计 LCM == n 的情况
        return;
    }
    for (int i = 1; i <= n; i++) // 枚举每个位置的取值
        dfs(dep + 1, lcm(cur, i));
}
int main() 
{
    freopen("lcm.in", "r", stdin);
    freopen("lcm.out", "w", stdout);
    int T; 
    cin >> T;
    while (T--) 
    {
        cin >> n >> m;
        ans = 0;
        dfs(1, 1); // 从 LCM = 1 开始枚举
        cout << ans << "\n";
    }
    return 0;
}
```

</details>

---

**$n$ 为质数时**

当 $n$ 为质数 $p$ 时，所有精灵的取值只能是 $1$ 或 $p$，否则 LCM 会超过 $p$。

要保证 LCM 恰好等于 $p$，至少要有一只精灵选择 $p$（不能全为 $1$）。

因此答案为：

\[
2^m - 1 \pmod{998244353}
\]

实现时使用**快速幂**与简单的**质数判定**即可，能轻松拿到额外的 10 分。

---

**满分做法**



对于任意两个正整数：

\[
\mathrm{lcm}(a,b)=\prod p_i^{\max(\alpha_i,\beta_i)}
\]

其中 $a=\prod p_i^{\alpha_i}$, $b=\prod p_i^{\beta_i}$。

该结论可以推广到多个数字，即：

> **多个数的 LCM 等于各个质因子指数的最大值所构成的乘积。**

---



设：

\[
n=p_1^{a_1}p_2^{a_2}\cdots p_k^{a_k}
\]


其中 $p_i$ 为质数，$a_i$ 为指数。

对于单个质因子 $p_i^{a_i}$：

* 每个数在该质因子上的指数可取 $\{0,1,\dots,a_i\}$，共有 $(a_i+1)^m$ 种取法；
* 若所有指数都小于 $a_i$，则 $\max < a_i$，方案数为 $a_i^m$；
* 因此恰好达到 $a_i$ 的方案数为：
  
\[
(a_i+1)^m - a_i^m
\]

各质因子相互独立，因此总方案数为：

\[
\boxed{
\text{Ans} = \prod_{i=1}^k \big((a_i+1)^m - a_i^m\big) \pmod{998244353}
}
\]

---


* **分解质因数**：$O(\sqrt{n})$
* **快速幂计算**：每次 $O(\log m)$
* **总复杂度**：$O(T \sqrt{n} \log m)$

---

<details>

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
constexpr int mod = 998244353;
i64 ksm(i64 a, i64 b)
{
    i64 res = 1;
    while (b)
    {
        if (b & 1) res = res * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return res;
}
void solve()
{
    i64 n, m;
    cin >> n >> m;
    i64 ans = 1;
    for (int i = 2; i <= n / i; i++)
    {
        if (n % i == 0)
        {
            int cnt = 0;
            while (n % i == 0) n /= i, cnt++;
            ans = ans * (ksm(cnt + 1, m) - ksm(cnt, m)) % mod;
        }
    }
    if (n > 1)
        ans = ans * (ksm(2, m) - 1) % mod;
    ans = (ans + mod) % mod;
    cout << ans << "\n";
}
int main()
{
    freopen("lcm.in", "r", stdin);
    freopen("lcm.out", "w", stdout);
    ios::sync_with_stdio(false), cin.tie(0);
    int t;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

</details>

---



## T4 旅游（graph）


**知识点：图论，最短路**


**状态设计（拆点思想）**

由于每次修改的次数有限（$k \le 30$），可以在最短路状态中加入“修改次数”这一维：

\[
\text{dis}[u][j] = \text{到达点 } u \text{ 还剩 } j \text{ 次修改的最短时间}
\]

那么整个图的状态空间是：

\[
\text{节点总数} = n \times (k+1)
\]

也可以理解为**对原图的每个点拆出 $k+1$ 个“层”**，第 $j$ 层代表“当前还剩 $j$”次修改机会。

---

**转移过程**

对于每条无向边 $(u,v,s,t,w)$，考虑从状态 $(u,j)$ 出发：

1. **不修改边的情况**
      * 记当前时间 $d$ 为 $dis[u][j]$
      * 若 $d < s$，则必须等到 $s$ 再出发；
      * 若 $d > t$，则不能再走这条边；
      * 否则，出发时间为 $\max(d,s)$，到达时间为 $\max(d,s) + w$。

   于是：

\[
dis[v][j] = \min(dis[v][j], \max(d,s) + w)
\]

   （注意若 $d > t$ 不可达）

1. **修改一次的情况（若还可修改）**
      * 可以将这条边的开放时间改为当前可走的样子：
          * 改 $s_i$ 为 $0$（表示随时可出发）；
          * 或改 $t_i$ 为极大值（表示永远可走）。
      * 实际上，这意味着可以立刻走，无需等待。

   所以：

\[
dis[v][j-1] = \min(dis[v][j-1], d + w)
\]


由于边权非负，使用 **Dijkstra** 算法。


---

**初始化与结果**

* 初始化所有 $\text{dis}[i][c]=\infty$
* 起点：$\text{dis}[1][0] = 0$
* 目标：$\min_{c=0}^{k} \text{dis}[n][c]$

即在所有可能使用修改次数的层中取最小值。


整体复杂度：$O(Tnk \log(nk))$


<details>

<summary>参考代码</summary>


```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using ary = array<i64, 4>;
template <typename T>
using vec = vector<T>;
constexpr int N = 2e5 + 5, mod = 998244353;

void solve()
{
    i64 n, m, k;
    cin >> n >> m >> k;
    vec<vec<ary>> e(n + 1);
    while (m--)
    {
        i64 x, y, s, t, w;
        cin >> x >> y >> s >> t >> w;
        e[x].push_back({y, w, s, t});
        e[y].push_back({x, w, s, t});
    }
    vec<vec<i64>> dis(n + 1, vec<i64>(k + 1, 1e18));
    // dis[i][j] : 走到 i 还剩 j 次机会的最短路
    vec<vec<bool>> vis(n + 1, vec<bool>(k + 1, 0));
    priority_queue<array<i64, 3>, vec<array<i64, 3>>, greater<array<i64, 3>>> q;
    dis[1][k] = 0;
    q.push({0, k, 1});
    while (q.size())
    {
        auto [d, j, u] = q.top(); // C++17 语法结构化绑定，信奥赛无法使用
        q.pop();
        if (vis[u][j]) continue;
        vis[u][j] = 1;
        for (auto [v, w, s, t] : e[u])  // C++17 语法结构化绑定，信奥赛无法使用
        {
            if (j > 0) // 可以使用修改的操作
            {
                if (dis[v][j - 1] > dis[u][j] + w)
                {
                    dis[v][j - 1] = dis[u][j] + w;
                    q.push({dis[v][j - 1], j - 1, v});
                }
            }
            if (dis[u][j] <= t) // 在截止时间内走这条边
            {
                if (dis[v][j] > max(d,s) + w)
                {
                    dis[v][j] = max(d,s) + w;
                    q.push({dis[v][j], j, v});
                }
            }
        }
    }
    i64 ans = *min_element(dis[n].begin(), dis[n].end());
    cout << ans << "\n";
}
int main()
{
    freopen("graph.in", "r", stdin); freopen("graph.out", "w", stdout);
    ios::sync_with_stdio(false), cin.tie(0);
    int t;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

</details>