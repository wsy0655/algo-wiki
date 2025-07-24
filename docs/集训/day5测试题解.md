## T1

考查：贪心

物品价格越大，其除以 $2$ 以后下取整优惠的就更多。

因此使用堆一直维护当前最贵的物品即可。

- 初始化将 $n$ 个商品的价格丢入大根堆。
- 循环迭代 $m$ 次
    - 每次取出堆顶，即当前最贵的物品。记其价格为 $x$。
    - 将 `x / 2` 放入堆中。相当于对该物品优惠一次。


最后累加堆中所有元素的值即为总价格。

时间复杂度：$O((n+m)\log{n})$


总价格注意开 `long long`。

```cpp
priority_queue<int> q;
for (int i = 1; i <= n; i++)
{
    int x;
    cin >> x;
    q.push(x);
}   
while (m--)
{
    int x = q.top();
    q.pop();
    q.push(x / 2);
}
ll ans = 0;
while (!q.empty())
{
    ans += q.top();
    q.pop();
}
cout << ans;
```

___

## T2


合并果子

___

## T3

考察点：贪心

使用小根堆，堆内存储三个值：`v x i` 分别代表：

- 当前函数值 $f(x)$
- 参数 $x$ 的取值。
- 第 $i$ 个函数。

初始化将 $f_1(1),f_2(1),\ldots,f_n(1)$ 放入堆中。


- 每次从堆内取出最小值以后，将 `f_i(x+1)` 的信息入堆即可。

迭代 $m$ 次得到最小的 $m$ 个函数值。

时间复杂度：$O((n+m)\log{n})$

```cpp
priority_queue<array<int, 3>, vector<array<int, 3>>, greater<array<int, 3>>> q;
for (int i = 1; i <= n; i++)
{
    cin >> a[i] >> b[i] >> c[i];
    // a[i] * x * x + b[i] * x + c[i]
    int v = a[i] * 1 * 1 + b[i] * 1 + c[i];
    q.push({v, i, 1});
}
while (m--)
{
    auto [v, i, x] = q.top();
    q.pop();
    cout << v << " ";
    v = a[i] * (x + 1) * (x + 1) + b[i] * (x + 1) + c[i];
    q.push({v, i, x + 1});
}
```


___

## T4


如果还剩 $1$ 天。那么你只能完成 $A_i=1$ 的任务。

- 显然完成 $B_i$ 更大的任务。



如果还剩 $2$ 天。那么你能完成 $A_i=1,A_i=2$ 的任务。

- 显然完成 $B_i$ 更大的任务。

因此维护剩余天数 $[1,m]$，贪心的选择当前天可以完成的最大任务。


**实现方法一：** 

- 按照 $A_i$ 从小到大排序所有任务。


- 枚举剩余天数 $[1,m]$，记作 $i$，当剩余天数为 $i$ 时，将符合要求的任务（双指针扫）加入 **大根堆**

- 使用大根堆挑选出剩余 $i$ 天时（第 $m-i$ 天） 能完成的最大收益的任务。




```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
using pii = pair<int, int>;
constexpr int N = 1e5 + 5;
pii a[N];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n, m;
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    {
        cin >> a[i].first >> a[i].second;
    }
    sort(a + 1, a + n + 1);
    priority_queue<int> q;
    ll ans = 0;
    for (int i = 1, j = 0; i <= m; i++) // 枚举剩余多少天
    {
        while (j + 1 <= n && a[j + 1].first <= i)
        {
            q.push(a[j + 1].second);
            j++;
        }
        if (!q.empty())
        {
            ans += q.top();
            q.pop();
        }
    }
    cout << ans;
    return 0;
}
```

**实现方法二：使用vector存储对应天数的任务如下所示**


```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
constexpr int N = 1e5 + 5;
vector<int> task[N];
int main()
{
    ios::sync_with_stdio(false), cin.tie(0);
    int n, m;
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
    {
        int a, b;
        cin >> a >> b;
        task[a].push_back(b);
    }
    priority_queue<int> q;
    ll ans = 0;
    for (int i = 1; i <= m; i++) // 枚举剩余多少天
    { 
        for (auto b : task[i]) q.push(b); // 添加剩余 i 天时可以完成的任务的收益 b
        if (!q.empty())
        {
            ans += q.top();
            q.pop();
        }
    }
    cout << ans;
    return 0;
}
```
