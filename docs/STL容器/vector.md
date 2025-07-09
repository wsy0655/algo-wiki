## 📚 STL 容器：vector

### 一、概述

`vector` 是 C++ STL 中的 **动态数组容器**，底层是 **内存连续** 的存储结构。

🚀 特点：

* ⚡ 支持随机访问（支持 `[]` 操作）
* 📈 自动扩容（插入时若空间不足）
* 🧩 插入/删除尾部元素复杂度为 \$O(1)\$（均摊）
* 🛠️ 插入/删除中间元素复杂度为 \$O(n)\$（需要移动元素）

📦 定义方式：

```cpp
vector<value_type> v;
```

🔧 示例：

```cpp
vector<int> v;                         // 整数 vector
vector<string> v;                      // 字符串 vector
vector<vector<int>> v;                // 二维 vector
vector<pair<int, int>> v;             // 元素为 pair 的 vector
```

📌 **注意：** `vector` 内存连续，可与数组指针兼容，常用于配合 `sort()`、`lower_bound()` 等函数。

---

### 二、基础操作

以下操作以 `vector<int> v` 为例：

| 操作               | 示例                    | 说明                        | 时间复杂度    |
| ------------------ | ----------------------- | --------------------------- | ------------- |
| 插入元素           | `v.push_back(x);`       | 在尾部插入元素              | \$O(1)\$ 均摊 |
| 删除尾部元素       | `v.pop_back();`         | 删除最后一个元素            | \$O(1)\$      |
| 获取元素个数       | `v.size();`             | 返回当前元素数量            | \$O(1)\$      |
| 清空所有元素       | `v.clear();`            | 清空所有元素                | \$O(n)\$      |
| 随机访问元素       | `v[i];`                 | 访问第 i 个元素（0-based）  | \$O(1)\$      |
| 获取首/尾元素      | `v.front();  v.back();` | 分别获取第一个/最后一个元素 | \$O(1)\$      |
| 判断是否为空       | `v.empty();`            | 是否为空                    | \$O(1)\$      |
| 替换全部内容       | `v.assign(n, val);`     | 重置为 n 个值为 val 的元素  | \$O(n)\$      |
| 与其他 vector 交换 | `v.swap(v2);`           | 与另一个 vector 交换内容    | \$O(1)\$      |

---

### 三、遍历方式

🧭 **方式一：下标遍历**

```cpp
for (int i = 0; i < v.size(); i++)
{
    cout << v[i] << " ";
}
```

🌀 **方式二：范围 for + auto**

```cpp
for (auto x : v)
{
    cout << x << " ";
}
```

🧲 **方式三：迭代器遍历**

```cpp
for (auto it = v.begin(); it != v.end(); ++it)
{
    cout << *it << " ";
}
```


---

### 四、进阶函数

```cpp
v.assign(5, 2);         // 重置为 5 个元素，每个值为 2
v.swap(v2);             // 与另一个 vector 交换内容
v == v2;                // 判断两个 vector 是否相等
```

---

### 五、常见算法函数

```cpp
sort(v.begin(), v.end());                     // 升序排序
sort(v.begin(), v.end(), greater<>());       // 降序排序
reverse(v.begin(), v.end());                 // 翻转 vector

int pos = lower_bound(v.begin(), v.end(), x) - v.begin(); // 第一个 >= x 的位置
int pos = upper_bound(v.begin(), v.end(), x) - v.begin(); // 第一个 > x 的位置
```

---

### 六、二维 vector

🧮 **方法一：数组嵌套 vector（行数固定）**

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 2e5 + 5; 
vector<int> a[N]; // 直接开 N 个 vector，相当于二维数组。
int main()
{
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++)
    {
        int m;
        cin >> m;
        for (int j = 0; j < m; j++)
        {
            int x;
            cin >> x;
            a[i].push_back(x); // 元素存到了 a[i][0] ~ a[i][m - 1]
        }
    }
    return 0;
}
```

🧮 **方法二：vector 嵌套 vector（行数可变）**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    int n;
    cin >> n;
    // 开 n 个 vector，编号为 1 到 n
    // vector 的类型是 vector 也是一个二维的
    vector<vector<int>> a(n + 1);
    for (int i = 1; i <= n; i++)
    {
        int m;
        cin >> m;
        for (int j = 0; j < m; j++)
        {
            int x;
            cin >> x;
            a[i].push_back(x); // 元素存到了 a[i][0] ~ a[i][m - 1]
        }
    }
    return 0;
}
```

🧮 **方法三：初始化为矩阵结构**

```cpp
int n, m;
cin >> n >> m;
vector<vector<int>> a(n, vector<int>(m, 0));
for (int i = 0; i < n; i++)
    for (int j = 0; j < m; j++)
        cin >> a[i][j];

vector<vector<int>> a(n + 1, vector<int>(m + 1, 0));
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        cin >> a[i][j];
```

---

### 七、emplace\_back vs push\_back

```cpp
v.push_back(MyClass(x, y));       // 先构造对象再插入
v.emplace_back(x, y);            // 直接在内部构造，更高效
```

- `emplace_back` 更推荐用于插入自定义结构体或类对象，性能更优。

---

### 八、总结对比

| 操作          | 是否支持 | 复杂度      |
| ------------- | -------- | ----------- |
| 随机访问      | ✅        | $O(1)$      |
| 尾部插入/删除 | ✅        | $O(1)$ 均摊 |
| 中间插入/删除 | ✅        | $O(n)$      |
| 自动扩容      | ✅        | -           |
| 内存连续      | ✅        | -           |

