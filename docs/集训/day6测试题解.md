
## T

知识点：倍增

$30$ 分暴力如下不再赘述：


```cpp
#include <bits/stdc++.h>
using namespace std;
constexpr int N = 2e5 + 5;
int n, x[N], a[N], ans[N];
long long k;
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    cin >> n >> k;
    for (int i = 1; i <= n; i++)
        cin >> x[i];
    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int j = 1; j <= k; j++)
    {
        for (int i = 1; i <= n; i++)
            ans[i] = a[x[i]];
        for (int i = 1; i <= n; i++)
            a[i] = ans[i];
    }
    for (int i = 1; i <= n; i++)
        cout << a[i] << " ";
    return 0;
}
```