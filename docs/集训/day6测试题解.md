
## T

知识点：倍增

首先有一个比较显然的暴力：对于每个 $i$，我们暴力枚举它跳 $k$ 次的位置，复杂度为 $O(nk)$，无法通过。


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 2e5 + 5;
int n, x[N], a[N], ans[N];
long long k;
int jump(int v, long long k)
{
    for (int i = 1; i <= k; i++)
        v = x[v];
    return v;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> x[i];
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= n; i++)
        cout << a[jump(i, k)] << " ";
    return 0;
}
```

使用倍增加速，令 $f_{i,j}$ 为 $i$ 跳了 $2^j$ 后的位置。

初始化：$f_{i,0}=x_i$，转移：$f_{i,j}=f_{f_{i,j-1},j-1}$

```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 2e5 + 5;
int n, f[N][60], x[N], a[N], ans[N];
long long k;
int jump(int v, long long k)
{
    for (int j = 0; j <= 59; j++)
        if (k >> j & 1)
            v = f[v][j]; 
    return v;
}
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> x[i];
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= n; i++)
        f[i][0] = x[i];
    for (int j = 1; j <= 59; j++)
        for (int i = 1; i <= n; i++)
            f[i][j] = f[f[i][j - 1]][j - 1];
    for (int i = 1; i <= n; i++)
        cout << a[jump(i, k)] << " ";
    return 0;
}
```


___

