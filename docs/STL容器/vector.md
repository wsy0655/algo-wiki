## vector

### 🧠 概述

`vector` 是 C++ STL 中的 **动态数组容器**，底层是 **内存连续** 的存储结构。

其核心特点是：

* ✅ 支持随机访问（可通过 `[]` 或 `.at()` 访问元素）；
* 🚀 自动扩容（插入元素时，空间不足会自动扩展）；
* ⚡ 尾部插入与删除效率高（\$O(1)\$ 均摊复杂度）；
* ⚠️ 插入/删除中间或开头元素效率较低（\$O(n)\$）。

**定义方式：**

```cpp
vector<value_type> v;
```

📦 示例：

```cpp
vector<int> v1;                     // 整型 vector
vector<string> v2;                  // 字符串 vector
vector<vector<int>> v3;            // 二维 vector
vector<pair<int, int>> v4;         // 存储 pair 的 vector
```

📦 初始化方法：

```cpp
vector<int> v = {1, 2, 3}; // 手动赋值
vector<int> v(n + 1); // 初始化长度为 n + 1 的 vector，元素全部为 0
vector<int> v(n, 1); // 初始化长度为 n 的 vector，元素全部为 1
```

📌 **提示：** vector 的内存是连续的，可以与数组互操作，例如传给 `sort()`。

---

### 🔧 基础操作

以下操作以 `vector<int> v` 为例：

| 功能          | 示例代码                   | 描述                       | 时间复杂度    |
| ------------- | -------------------------- | -------------------------- | ------------- |
| 尾部插入      | `v.push_back(x);`          | 添加元素到末尾             | \$O(1)\$ 均摊 |
| 尾部删除      | `v.pop_back();`            | 删除最后一个元素           | \$O(1)\$      |
| 获取元素个数  | `v.size();`                | 返回当前元素数量           | \$O(1)\$      |
| 判断是否为空  | `v.empty();`               | 返回是否为空               | \$O(1)\$      |
| 清空元素      | `v.clear();`               | 清空所有元素               | \$O(n)\$      |
| 随机访问      | `v[i];` / `v.at(i);`       | 获取第 i 个元素（0-based） | \$O(1)\$      |
| 获取首/尾元素 | `v.front();` / `v.back();` | 获取第一个/最后一个元素    | \$O(1)\$      |
| 替换内容      | `v.assign(n, val);`        | 设置为 n 个 val 元素       | \$O(n)\$      |
| 交换内容      | `v.swap(v2);`              | 与另一个 vector 交换       | \$O(1)\$      |
| 判断相等      | `v == v2`                  | 判断两个 vector 是否相等   | \$O(n)\$      |

---

### 🔁 遍历方式

🧭 **方法一：下标访问**

```cpp
for (int i = 0; i < v.size(); i++) 
{
    cout << v[i] << " ";
}
```

🌀 **方法二：范围 for 循环**

```cpp
for (auto x : v) 
{
    cout << x << " ";
}
```

🧲 **方法三：迭代器遍历**

```cpp
for (auto it = v.begin(); it != v.end(); it++) 
{
    cout << *it << " ";
}
```

---

### ✨ 常用算法函数

```cpp
sort(v.begin(), v.end());                     // 升序排序
sort(v.begin(), v.end(), greater<>());       // 降序排序
reverse(v.begin(), v.end());                 // 翻转 vector

int pos = lower_bound(v.begin(), v.end(), x) - v.begin(); // 第一个 >= x 的位置
int pos = upper_bound(v.begin(), v.end(), x) - v.begin(); // 第一个 > x 的位置
```

---

### 🧩 二维 vector 用法

#### ✅ 方法一：数组嵌套 vector（行数固定）

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 2e5 + 5; 
vector<int> a[N];

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
            a[i].push_back(x);
        }
    }
    return 0;
}
```

#### ✅ 方法二：vector 嵌套 vector（行数可变）

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() 
{
    int n;
    cin >> n;
    vector<vector<int>> a(n + 1);
    for (int i = 1; i <= n; i++) 
    {
        int m;
        cin >> m;
        for (int j = 0; j < m; j++) 
        {
            int x;
            cin >> x;
            a[i].push_back(x);
        }
    }
    return 0;
}
```

#### ✅ 方法三：初始化二维矩阵

```cpp
int n, m;
cin >> n >> m;
vector<vector<int>> a(n, vector<int>(m, 0));

for (int i = 0; i < n; i++)
    for (int j = 0; j < m; j++)
        cin >> a[i][j];

// 或使用 1-based 编号：
vector<vector<int>> b(n + 1, vector<int>(m + 1, 0));
for (int i = 1; i <= n; i++)
    for (int j = 1; j <= m; j++)
        cin >> b[i][j];
```

---

### 🆚 emplace\_back vs push\_back

```cpp
v.push_back(MyClass(x, y));       // 先构造临时对象再复制进 vector
v.emplace_back(x, y);             // 直接在 vector 内构造，更高效
```

✅ 更推荐 `emplace_back`，特别是用于结构体、类对象插入。

---

### 📌 总结对比

| 特性                | vector |
| ------------------- | ------ |
| 支持随机访问        | ✅      |
| 尾部插入/删除效率高 | ✅      |
| 中间插入/删除效率低 | ⚠️      |
| 自动扩容            | ✅      |
| 内存连续            | ✅      |
| 与数组互通          | ✅      |

---

🧩 **适用场景**：

* 需要快速访问元素（支持 `[]`）；
* 常从尾部插入或删除；
* 对内存连续性有要求（如配合 `sort()` 使用）。


