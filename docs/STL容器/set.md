### 🌲 一、概述

`set` 是 C++ STL 中的关联容器，含有键值类型对象的已排序集合，内部通常采用 **红黑树** 实现。

🧠 特点：

* 元素自动升序排序；
* 不允许重复元素；
* 插入、删除、查找复杂度为 \$O(\log n)\$；
* 适用于需要快速查找、插入和删除的场景。

📌 定义方式：

```cpp
set<value_type> s;
```

📍 常见示例：

```cpp
set<int> s;
set<long long> s;
set<string> s;
set<pair<int, int>> s;
set<int> s[100005]; // 多个 set
```

📉 若需降序排序：

```cpp
set<int, greater<>> s;
```

🔎 注意：类型必须支持 `<` 运算符。若使用自定义类型作键，需自定义比较函数。

---

### 🧰 二、基础操作

以下操作以 `set<int> s` 为例：

| 操作       | 示例           | 说明                                          | 复杂度        |
| ---------- | -------------- | --------------------------------------------- | ------------- |
| 插入元素   | `s.insert(x);` | 若 `x` 不存在则插入                           | \$O(\log n)\$ |
| 存在性查询 | `s.count(x);`  | 返回 1 表示存在，返回 0 表示不存在            | \$O(\log n)\$ |
| 查找迭代器 | `s.find(x);`   | 返回指向 `x` 的迭代器，若不存在返回 `s.end()` | \$O(\log n)\$ |
| 删除值     | `s.erase(x);`  | 删除所有值为 `x` 的元素                       | \$O(\log n)\$ |
| 删除迭代器 | `s.erase(it);` | 删除指定迭代器位置的元素（需存在）            | \$O(\log n)\$ |
| 清空       | `s.clear();`   | 清空所有元素                                  | \$O(n)\$      |
| 获取大小   | `s.size();`    | 返回当前元素数量                              | \$O(1)\$      |

⚠️ 注意：涉及迭代器的操作，如 `s.erase(s.begin())`，在集合为空时会导致运行错误（RE），务必先判断是否为空。

---

### 🔁 三、遍历方式

📘 方法一：传统迭代器写法

```cpp
for (set<int>::iterator it = s.begin(); it != s.end(); ++it)
{
    cout << *it << " ";
}
```

📗 方法二：使用 `auto`

```cpp
for (auto x : s)
{
    cout << x << " ";
}
```

✅ 推荐使用 `auto` 简洁明了。

---

### 🧮 四、函数与技巧

* 查找元素：

```cpp
if (s.find(x) != s.end())
    cout << "Found";
```

* `lower_bound(x)`：返回第一个大于等于 \$x\$ 的元素的迭代器

```cpp
auto it = s.lower_bound(x);
```

* `upper_bound(x)`：返回第一个大于 \$x\$ 的元素的迭代器

```cpp
auto it = s.upper_bound(x);
```

📉 最小值：

```cpp
if (!s.empty())
    cout << *s.begin();
```

📈 最大值：

```cpp
if (!s.empty()) 
{
    auto it = s.end();
    --it;
    cout << *it;
}
```

或

```cpp
if (!s.empty())
    cout << *s.rbegin();
```

🗑️ 删除最小值：

```cpp
if (!s.empty()) s.erase(s.begin());
```

🗑️ 删除最大值：

```cpp
if (!s.empty()) 
{
    auto it = s.end();
    --it;
    s.erase(it);
}
```

📦 支持复杂类型：以 `set<pair<int, int>>` 为例：

```cpp
set<pair<int, int>> s;
s.insert({1, 2});
s.insert({3, 4});
s.insert({5, 6});

s.erase({1, 2}); // 按照值删除

auto it = s.find({1, 2}); 
if (it != s.end()) s.erase(it); // 按照迭代器删除
// 遍历 pair<int, int> 类型的 set 
for (auto it : s)
{
    cout << it.first << " " << it.second << "\n";	
} 

// 获取最小值

if (!s.empty())
{
    auto it = s.begin();
    cout << (*it).first << " " << (*it).second << "\n";
} 
```

---

### 🧪 五、模板题示例

🔗 [模板题链接](http://www.tarjanoj.com/d/tarjan24/p/180)

```cpp
#include <bits/stdc++.h>
using namespace std;
int main()
{
    set<int> s;
    int q;
    cin >> q;
    while (q--)
    {
        int op;
        cin >> op;
        if (op == 1)
        {
            int x; 
            cin >> x;
            s.insert(x);
        }
        else if (op == 2)
        {
            int x; 
            cin >> x;
            s.erase(x);
        }
        else if (op == 3)
        {
            int x, y;
            cin >> x >> y;
            vector<int> temp; // 存储待删除元素，不能直接遍历时删除
            for (auto val : s)
                if (val >= x && val <= y)
                    temp.push_back(val);
            for (auto val : temp)
                s.erase(val);
        }
        else if (op == 4)
        {
            if (s.empty()) cout << "-1\n";
            else cout << *s.begin() << "\n";
        }
    }
    return 0;
}
```

---

### 🌀 六、`multiset`

`multiset` 是允许重复元素的有序集合，底层同样为红黑树。

📌 特点：

* 自动排序（默认升序）；
* 支持重复值；
* 插入、查找、删除复杂度均为 \$O(\log n)\$；

📍 操作：

```cpp
multiset<int> s;
s.insert(x); // 插入
s.count(x);  // 查询出现次数
s.erase(x);  // 删除所有值为 x 的元素
```

🔍 若只删除一个值为 x 的元素：

```cpp
auto it = s.find(x);
if (it != s.end()) s.erase(it); // 删除一个
```

---

### ⚙️ 七、其它类型 set 容器

| 容器类型             | 是否有序 | 是否唯一 | 允许重复 | 查找复杂度                  |
| -------------------- | -------- | -------- | -------- | --------------------------- |
| `set`                | ✅ 是     | ✅ 是     | ❌ 否     | \$O(\log n)\$               |
| `multiset`           | ✅ 是     | ❌ 否     | ✅ 是     | \$O(\log n)\$               |
| `unordered_set`      | ❌ 否     | ✅ 是     | ❌ 否     | 平均 \$O(1)\$ 最坏 \$O(n)\$ |
| `unordered_multiset` | ❌ 否     | ❌ 否     | ✅ 是     | 平均 \$O(1)\$ 最坏 \$O(n)\$ |

✅ 在多数算法竞赛中首选 `set` 和 `multiset`，因其有序性。

---

📚 **总结**：

* `set` 适用于快速查找且不重复元素的场景；
* `multiset` 用于允许重复的计数类任务；
* `unordered_set` 不保证顺序但效率更高。

💡 熟练掌握 `set` 与其变种容器，将大幅提高你解决算法问题的能力！
