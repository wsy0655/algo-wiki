## T1

知识点：循环

<details>

<summary>参考代码</summary>

```cpp
int ans = x;
while (x != 0)
{
    ans += x / y * 2;
    x /= y;
}   
cout << ans;
```



</details>


___


## T2

知识点：循环，闰年判断

<details>

<summary>参考代码</summary>

```cpp
bool leap(int y)
{
    return (y % 4 == 0 && y % 100 != 0) || (y % 400 == 0);
}
int ans = 0;
for (int i = l; i <= r; i++)
{
    if (leap(i))
    {
        ans += 366;
    }
}
```
</details>

___


## T3

知识点：字符串，转义字符。


核心思路：构造出正确的输入输出字符串，然后和输入给出的进行比较即可。

<details>

<summary>参考代码</summary>

```cpp
string in = "freopen(\"" + s + ".in\",\"r\",stdin);";
string out = "freopen(\"" + s + ".out\",\"w\",stdout);";
if (in == a && out == b)
    cout << "Yes";
else
    cout << "No";
```

</details>

___


## T4

知识点：数学

思路：求出恰好大于等于 $n$ 的超级奇数，然后减去 $n$ 即可。

枚举 $n$ 往后的所有整数，一一判断显然会 TLE。可以通过构造的方式找到超级奇数。

- 从高位开始找到第一个是偶数的数位，记作是 $i$。
- 第 $i$ 位以前，原封不动。
- 第 $i$ 位的值加 $1$ 变为奇数。
- 第 $i$ 位以后，全部变为 $1$。

如此构造即可得到恰好大于等于 $n$ 的超级奇数。时间复杂度相当于遍历一次 $n$ 的所有数位。

<details>

<summary>参考代码</summary>

```cpp
long long solve(long long n)
{
    string s = to_string(n);
    for (int i = 0; i < s.size(); i++)
    {
        int x = s[i] - '0';
        if (x % 2 == 0)
        {
            s[i] += 1;
            for (int j = i + 1; j < s.size(); j++)
            {
                s[j] = '1';
            }
            break;
        }
    }
    return stoll(s);
}
```
</details>

___


## T5

知识点：和式化简，前缀和。


定义 $s_i=\sum\limits_{j=1}^i a_j$，则 $a_i+\ldots + a_j=s_j-s_{i-1}$。

那么

$$
\begin{aligned}
ans
&= \sum\limits_{i=l}^r \sum\limits_{j=i}^r (a_i+a_{i+1}+\ldots+a_j)\\
&=\sum\limits_{i=l}^r \sum\limits_{j=i}^r (s_{j}-s_{i-1})
\end{aligned}
$$

首先利用和式的可拆分性质可以得到

$$
\begin{aligned}
ans
&=\sum\limits_{i=l}^r \sum\limits_{j=i}^r s_{j}-\sum\limits_{i=l}^r \sum\limits_{j=i}^r s_{i-1}.
\end{aligned}
$$

不难看出 $\sum\limits_{j=i}^r s_{j}$ 可以用前缀和的前缀和表示。因此定义

* $S_1[i]=\sum\limits_{j=1}^i s_j$。

则

$$
\sum\limits_{j=i}^r s_{j}=S_1[r]-S_1[i-1].
$$

那么第一部分就变为

$$
\sum\limits_{i=l}^r\big(S_1[r]-S_1[i-1]\big)
= S_1[r]\times (r-l+1)\;-\;\sum\limits_{i=l}^r S_1[i-1].
$$

把 $\sum\limits_{i=l}^r S_1[i-1]$ 也写成前缀和的前缀和。定义

* $S_2[i]=\sum\limits_{j=1}^i S_1[j]$,

则

$$
\sum\limits_{i=l}^r S_1[i-1]=\sum\limits_{t=l-1}^{r-1} S_1[t]=S_2[r-1]-S_2[l-2].
$$

于是第一部分为

$$
\boxed{\;\sum\limits_{i=l}^r \sum\limits_{j=i}^r s_{j}
= S_1[r]\,(r-l+1)\;-\;\big(S_2[r-1]-S_2[l-2]\big)\;}
$$

同理得到

$$
\begin{aligned}
\sum\limits_{i=l}^r \sum\limits_{j=i}^r s_{i-1}
&= \sum\limits_{i=l}^r s_{i-1}\cdot (r-i+1)\\
&= r\cdot \sum\limits_{i=l}^r s_{i-1}-\sum\limits_{i=l}^r (i-1)\,s_{i-1}.
\end{aligned}
$$

注意这里

$$
\sum\limits_{i=l}^r s_{i-1}=\sum\limits_{t=l-1}^{r-1} s_t
= S_1[r-1]-S_1[l-2],
$$

而 $\sum\limits_{i=l}^r (i-1)\,s_{i-1}=\sum\limits_{t=l-1}^{r-1} t\,s_t$ 需要“带权”的前缀和。定义

* $S_3[i]=\sum\limits_{j=1}^i j\cdot s_j$,

则

$$
\sum\limits_{i=l}^r (i-1)\,s_{i-1}=S_3[r-1]-S_3[l-2].
$$

于是第二部分为

$$
\boxed{\;\sum\limits_{i=l}^r \sum\limits_{j=i}^r s_{i-1}
= r\cdot \big(S_1[r-1]-S_1[l-2]\big)\;-\;\big(S_3[r-1]-S_3[l-2]\big)\;}
$$

**总结：**

$$
\boxed{
\begin{aligned}
ans
&=\Big[S_1[r]\,(r-l+1)\;-\;\big(S_2[r-1]-S_2[l-2]\big)\Big]\\
&\quad-\Big[r\cdot \big(S_1[r-1]-S_1[l-2]\big)\;-\;\big(S_3[r-1]-S_3[l-2]\big)\Big]\\
&=S_1[r]\,(r-l+1)\;-\;\big(S_2[r-1]-S_2[l-2]\big)\;-\;r\big(S_1[r-1]-S_1[l-2]\big)\;+\;\big(S_3[r-1]-S_3[l-2]\big).
\end{aligned}}
$$

其中

* $s_i=a_1+a_{2}+\ldots+a_i$
* $S_1[i]=\sum\limits_{j=1}^i s_j$
* $S_2[i]=\sum\limits_{j=1}^i S_1[j]$
* $S_3[i]=\sum\limits_{j=1}^i j\cdot s_j$

并约定越界下标（$\le 0$）时这些量为 0（如 $S_1[0]=S_2[0]=S_3[0]=0$）。

<details>

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
using int64 = long long;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n, q;
    cin >> n >> q;
    vector<int64> a(n + 1), s(n + 1), S1(n + 1), S2(n + 1), S3(n + 1);
    // 输入
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    // 预处理
    for (int i = 1; i <= n; i++)
    {
        s[i] = s[i - 1] + a[i];             // 前缀和
        S1[i] = S1[i - 1] + s[i];           // s 的前缀和
        S2[i] = S2[i - 1] + S1[i];          // S1 的前缀和
        S3[i] = S3[i - 1] + 1LL * i * s[i]; // 带权前缀和
    }
    while (q--)
    {
        int l, r;
        cin >> l >> r;
        // 注意边界：当 l = 1 时 l-2 会是 -1，下标要安全处理
        auto get = [&](const vector<int64> &arr, int idx)
        {
            if (idx < 0)
                return 0LL;
            return arr[idx];
        };
        int64 part1 = S1[r] * (r - l + 1) - (get(S2, r - 1) - get(S2, l - 2));
        int64 part2 = 1LL * r * (get(S1, r - 1) - get(S1, l - 2)) - (get(S3, r - 1) - get(S3, l - 2));
        int64 ans = part1 - part2;
        cout << ans << "\n";
    }
    return 0;
}
```
</details>


___


## T6

知识点：拆点 BFS

<details>

<summary>参考代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
using pii = pair<int, int>;
template <typename T>
using vec = vector<T>;
constexpr int N = 2e5 + 5, mod = 998244353;

signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n, m;
    cin >> n >> m;
    vec<vec<char>> a(n + 1, vec<char>(m + 1));
    int x1 = 0, y1 = 0, x2 = 0, y2 = 0;
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            cin >> a[i][j];
            if (a[i][j] == 'S')
            {
                x1 = i, y1 = j;
            }
            if (a[i][j] == 'E')
            {
                x2 = i, y2 = j;
            }
        }
    }


    const int dx[] = {0, 1, 0, -1}, dy[] = {1, 0, -1, 0};
    vec<vec<vec<vec<int>>>> dis(n + 1, vec<vec<vec<int>>>(m + 1, vec<vec<int>>(4, vec<int>(4, 1e9))));
    queue<array<int, 4>> q;
    for (int i = 0; i < 4; i++)
    {
        dis[x1][y1][i][1] = 0;
        q.push({x1, y1, i, 1});
    }

    while (!q.empty())
    {
        auto [x, y, fx, d] = q.front();
        q.pop();
        for (int i = 0; i < 4; i++)
        {
            int nx = x + dx[i], ny = y + dy[i];
            if (nx < 1 || nx > n || ny < 1 || ny > m || a[nx][ny] == '#')
                continue;
            if (i == fx)
            {
                if (d < 3)
                {
                    if (dis[nx][ny][i][d + 1] == 1e9)
                    {
                        dis[nx][ny][i][d + 1] = dis[x][y][fx][d] + 1;
                        q.push({nx, ny, i, d + 1});
                    }
                }
            }
            else
            {
                if (dis[nx][ny][i][1] == 1e9)
                {
                    dis[nx][ny][i][1] = dis[x][y][fx][d] + 1;
                    q.push({nx, ny, i, 1});
                }
            }
        }
    }
    int ans = 1e9;
    for (int i = 0; i < 4; i++)
        for (int j = 1; j <= 3; j++)
            ans = min(ans, dis[x2][y2][i][j]);
    if (ans == 1e9) ans = -1;
    cout << ans;
    return 0;
}
```
</details>

___


## T7

知识点：差分，前缀和。


<details>

<summary>参考代码</summary>

```cpp
while (m--)
{
    int l, r;
    cin >> l >> r;
    int len = r - l + 1;
    a[l] += 1;
    if (l + 1 <= n)
        a[l + 1] += 1;
    if (r + 1 <= n)
        a[r + 1] += -Z(len + 1) * (len + 1);
    if (r + 2 <= n)
        a[r + 2] += Z(2) * len * len + 2 * len - 1;
    if (r + 3 <= n)
        a[r + 3] += -Z(len) * len;
}   
for (int i = 1; i <= n; i++)
    a[i] += a[i - 1];
for (int i = 1; i <= n; i++)
    a[i] += a[i - 1];
for (int i = 1; i <= n; i++)
    a[i] += a[i - 1];
for (int i = 1; i <= n; i++)
    cout << a[i] << " ";
```

</details>
___