## [ABC410F] Balanced Rectangles


使用了 `unordered_map` 常数较大，导致 TLE。

<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using pii = pair<int, int>;
using pli = pair<i64, int>;
template <typename T>
using vec = vector<T>;
constexpr int N = 2e5 + 5, mod = 998244353;

void solve()
{
    int n, m;
    cin >> n >> m;
    vec<vec<int>> a(n + 1, vec<int>(m + 1, 0));
    vec<vec<int>> d(n + 1, vec<int>(m + 1, 0));
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            char x;
            cin >> x;
            d[i][j] = d[i - 1][j] + (x == '#' ? 1 : -1);
        }
    }

    i64 ans = 0;
    for (int x1 = 1; x1 <= n; x1++)
    {
        for (int x2 = x1; x2 <= n; x2++)
        {
            vec<int> s(m + 1, 0);
            unordered_map<int, int> cnt;
            cnt[0] = 1;
            for (int y = 1; y <= m; y++)
            {
                s[y] = s[y - 1] + d[x2][y] - d[x1 - 1][y];
                ans += cnt[s[y]];
                cnt[s[y]]++;
            }
        }
    }
    cout << ans << "\n";
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int t;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

</details>


使用数组动态重置需要更新的位置，速度更快。


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
using i64 = long long;
using pii = pair<int, int>;
using pli = pair<i64, int>;
template <typename T>
using vec = vector<T>;
constexpr int N = 2e5 + 5, mod = 998244353;

void solve()
{
    int n, m;
    cin >> n >> m;
    vec<vec<int>> a(n + 1, vec<int>(m + 1, 0));
    vec<vec<int>> d(n + 1, vec<int>(m + 1, 0));
    for (int i = 1; i <= n; i++)
    {
        for (int j = 1; j <= m; j++)
        {
            char x;
            cin >> x;
            d[i][j] = d[i - 1][j] + (x == '#' ? 1 : -1);
            a[i][j] = a[i][j - 1] + (x == '#' ? 1 : -1);
        }
    }
    vec<int> cnt(2 * n * m + 1, 0);
    i64 ans = 0;
    if (n <= m)
    {
        for (int x1 = 1; x1 <= n; x1++)
        {
            for (int x2 = x1; x2 <= n; x2++)
            {
                int s = 0;  
                cnt[0 + n * m] = 1;
                for (int y = 1; y <= m; y++)
                {
                    s = s + d[x2][y] - d[x1 - 1][y];
                    ans += cnt[s + n * m];
                    cnt[s + n * m]++;
                }

                s = 0;
                cnt[0 + n * m] = 0;
                for (int y = 1; y <= m; y++)
                {
                    s = s + d[x2][y] - d[x1 - 1][y];
                    cnt[s + n * m]--;
                }
            }
        }
    }
    else
    {
        for (int y1 = 1; y1 <= m; y1++)
        {
            for (int y2 = y1; y2 <= m; y2++)
            {
                int s = 0;
                cnt[0 + n * m] = 1;
                for (int x = 1; x <= n; x++)
                {
                    s = s + a[x][y2] - a[x][y1 - 1];
                    ans += cnt[s + n * m];
                    cnt[s + n * m]++;
                }

                s = 0;
                cnt[0 + n * m] = 0;
                for (int x = 1; x <= n; x++)
                {
                    s = s + a[x][y2] - a[x][y1 - 1];
                    cnt[s + n * m]--;
                }
            }
        }
    }
    cout << ans << "\n";
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int t;
    cin >> t;
    while (t--) solve();
    return 0;
}
```

</details>
____


##  计数单元


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
int n, m, a[1000006];
constexpr int mod = 998244353;
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    while (m--)
    {
        int l, r;
        cin >> l >> r;
        int len = r - l + 1;
        a[l] = (a[l] + 1) % mod;
        a[l + 1] = (a[l + 1] + 1) % mod;
        a[r + 1] = (a[r + 1] + (-(len * len) - 2 * len - 1)) % mod;
        a[r + 2] = (a[r + 2] + 2 * len * len + 2 * len - 1) % mod;
        a[r + 3] = (a[r + 3] - (len * len)) % mod;
    }
    for (int i = 1; i <= n; i++)
        a[i] = (a[i] + a[i - 1]) % mod;
    for (int i = 1; i <= n; i++)
        a[i] = (a[i] + a[i - 1]) % mod;
    for (int i = 1; i <= n; i++)
        a[i] = (a[i] + a[i - 1]) % mod;
    for (int i = 1; i <= n; i++)
        cout << (a[i] + mod) % mod << " ";
    return 0;
}
```

</details>
____


## pay


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int N = 1e6 + 5;
int n, m, a[N], b[N], c[N];
bool check(int k)
{
    for (int i = 1; i <= n; i++)
        c[i] = 0;
    for (int i = 1; i <= m; i++)
    {
        if (b[i] - k >= 0)
        {
            c[b[i] - k + 1] += 1;
            c[b[i] + 1] += -2;
        }
        else
        {
            c[1] += k - (b[i] - 1);
            c[2] += 1 - (k - (b[i] - 1));
            c[b[i] + 1] += -2;
        }
        if (b[i] + k <= n + 1)
        {
            c[b[i] + k + 1] += 1;
        }
    }
    for (int i = 1; i <= n; i++)
        c[i] += c[i - 1];
    for (int i = 1; i <= n; i++)
    {
        c[i] += c[i - 1];
        if (c[i] < a[i]) return false;
    }
    return true;
}
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= m; i++)
        cin >> b[i];
    int l = 0, r = 1e10, ans = 0;
    while (l <= r)
    {
        int mid = (l + r) >> 1;
        if (check(mid))
        {
            ans = mid;
            r = mid - 1;
        }
        else
        {
            l = mid + 1;
        }
    }
    cout << ans;
    return 0;
}
```

</details>


## [NOIP 2022] 种花



<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
constexpr int mod = 998244353;
int t, id, n, m, c, f, r[1005][1005], d[1005][1005], a[1005][1005], sum[1005][1005], C, F;
signed main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> t >> id;
    while (t--)
    {
        memset(r, 0, sizeof(r));
        memset(d, 0, sizeof(d));
        memset(a, 0, sizeof(a));
        memset(sum, 0, sizeof(sum));
        cin >> n >> m >> c >> f;
        C = 0, F = 0;
        for (int i = 1; i <= n; i++)
        {
            for (int j = 1; j <= m; j++)
            {
                char c;
                cin >> c;
                a[i][j] = c - '0';
            }
        }
        for (int i = 1; i <= n; i++)
        {
            for (int j = m; j >= 1; j--)
            {
                r[i][j] = (a[i][j] ? 0 : r[i][j + 1] + 1);
            }
        }
        for (int i = n; i >= 1; i--)
        {
            for (int j = 1; j <= m; j++)
            {
                d[i][j] = (a[i][j] ? 0 : d[i + 1][j] + 1);
            }
        }
        for (int x2 = 3; x2 <= n; x2++)
        {
            for (int y0 = 1; y0 <= m; y0++)
            {
                if (d[x2 - 2][y0] >= 3)
                {
                    sum[x2][y0] = (sum[x2 - 1][y0] + r[x2 - 2][y0] - 1) % mod;
                }
            }
        }
        for (int x2 = 3; x2 <= n; x2++)
        {
            for (int y0 = 1; y0 <= m; y0++)
            {
                C = (C + sum[x2][y0] * (r[x2][y0] - 1)) % mod;
                F = (F + sum[x2][y0] * (r[x2][y0] - 1) % mod * (d[x2][y0] - 1)) % mod;
            }
        }
        cout << c * C << " " << f * F << "\n";
    }
    return 0;
}
```

</details>


## [ABC236E] Average and Median


<details>

<summary>完整代码</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 1e5 + 5;
const double eps = 1e-6;
int n, a[N];
bool check(double x)
{
    vector<double> b(n + 1);
    for (int i = 1; i <= n; i++) b[i] = a[i] - x;
    vector<double> f(n + 1);
    f[1] = b[1];
    for (int i = 2; i <= n; i++) 
    {
        f[i] = max(f[i - 1], f[i - 2]) + b[i];
    }
    return max(f[n - 1], f[n]) >= 0;
}
bool check(int x)
{
    vector<int> b(n + 1);
    for (int i = 1; i <= n; i++) b[i] = (a[i] >= x ? 1 : -1);
    vector<int> f(n + 1);
    f[1] = b[1];
    for (int i = 2; i <= n; i++) 
    {
        f[i] = max(f[i - 1], f[i - 2]) + b[i];
    }
    return max(f[n - 1], f[n]) > 0;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n;
    for (int i = 1; i <= n; i++) cin >> a[i];
    double l = 0, r = 1e9, ans = 0;
    while (l + eps < r)
    {
        double mid = (l + r) / 2;
        if (check(mid)) ans = mid, l = mid;
        else r = mid;
    }
    cout << fixed << setprecision(10) << ans << "\n";
    l = 0, r = 1e9, ans = 0;
    while (l <= r)
    {
        int mid = (l + r) / 2;
        if (check(mid)) ans = mid, l = mid + 1;
        else r = mid - 1;
    }
    cout << fixed << setprecision(0) << ans << " ";
    return 0;
}
```

</details>


## 


<details>

<summary>完整代码</summary>


```cpp

```

</details>

