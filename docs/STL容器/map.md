### 🌐 一、概述

`map` 是 C++ STL 中的有序键值对容器，底层实现为 **红黑树**。

📌 **特点：**

* 元素的 **键唯一**；
* 支持按键自动排序（升序）；
* 插入、删除、查找复杂度为 \$O(\log n)\$。

📦 **定义方式：**

```cpp
map<key_type, value_type> mp;
```

🔸 **常见示例：**

```cpp
map<int, int> mp;                     // 键和值均为 int
map<string, int> mp;                 // 键为 string，值为 int
map<vector<int>, int> mp;           // 键为 vector<int>
map<int, vector<int>> mp;           // 值为 vector<int>
map<int, map<int, int>> mp;         // 二维 map
```

⚠️ **注意：** 键类型必须支持 `<` 比较运算符。若为自定义类型，需提供比较函数。

---

### ⚙️ 二、基础操作

以下操作以 `map<int, int> mp` 为例：

| 操作      | 示例           | 说明                          | 复杂度        |
| --------- | -------------- | ----------------------------- | ------------- |
| 插入/修改 | `mp[x] = y;`   | 若 `x` 不存在则插入，否则修改 | \$O(\log n)\$ |
| 查找键    | `mp.count(x)`  | 存在返回 1，不存在返回 0      | \$O(\log n)\$ |
| 删除键    | `mp.erase(x);` | 删除键为 `x` 的元素           | \$O(\log n)\$ |
| 获取键数  | `mp.size();`   | 返回键的数量                  | \$O(1)\$      |
| 清空      | `mp.clear();`  | 清空所有元素                  | \$O(n)\$      |

---

### 🔁 三、遍历方式

🔸 **方法一：传统迭代器写法**

```cpp
for (map<int, int>::iterator it = mp.begin(); it != mp.end(); ++it)
{
    cout << it->first << " " << it->second << "\n";
}
```

🔸 **方法二：使用 `auto` 自动类型推导**

```cpp
for (auto it : mp)
{
    cout << it.first << " " << it.second << "\n";
}
```

---

### 📊 四、常见应用场景

📌 **例 1：统计整数频次（元素较小）**

```cpp
int cnt[100005];
for (int i = 1; i <= n; i++)
{
    int x;
    cin >> x;
    cnt[x]++;
}
```

* 适用于 \$1 \leq x \leq 10^7\$，用数组更快。

📌 **例 2：统计整数频次（元素较大）**

```cpp
map<int, int> cnt;
for (int i = 1; i <= n; i++)
{
    int x;
    cin >> x;
    cnt[x]++;
}
```

* 适用于 \$x\$ 范围很大，空间利用率高，按需开辟。

📌 **例 3：统计字符串频次**

```cpp
map<string, int> cnt;
for (int i = 1; i <= n; i++)
{
    string s;
    cin >> s;
    cnt[s]++;
}
```

---

### 🔎 五、算法函数（利用有序特性）

以 `map<int, int> mp` 为例：

🔸 `lower_bound(x)`：返回第一个键 $\geq x$ 的迭代器。

```cpp
auto it = mp.lower_bound(x);
if (it != mp.end())
{
    cout << it->first << " " << it->second;
}
```

🔸 `upper_bound(x)`：返回第一个键 $> x$ 的迭代器。

```cpp
auto it = mp.upper_bound(x);
if (it != mp.end())
{
    cout << it->first << " " << it->second;
}
```

---

### 🧱 六、二维 map

🔸 **方法一：数组嵌套 map**

```cpp
map<int, int> mp[100005];
mp[i][j] = k;
```

含义：第 \$i\$ 个 map 中，键 \$j\$ 的值为 \$k\$。

🔸 **方法二：map 嵌套 map**

```cpp
map<int, map<int, int>> mp;
mp[i][j] = k;
```

含义：外层键为 \$i\$，内层键为 \$j\$，对应值为 \$k\$。

---

### ⚡ 七、unordered\_map（无序版本）

`unordered_map` 是基于 **哈希表** 实现的 map，平均复杂度更优。

📌 特点：

* 键无序
* 平均操作复杂度 \$O(1)\$，最坏 \$O(n)\$（哈希冲突）

```cpp
unordered_map<int, int> ump;
ump[x] = y;
```

✅ **支持的操作：**

* `ump[x] = y;` 创建或修改
* `ump.count(x);` 判断是否存在
* `ump.erase(x);` 删除
* `ump.size();` 获取大小
* `ump.clear();` 清空

⚠️ **不支持** `lower_bound()` 与 `upper_bound()`。

---

### ♻️ 八、multimap（可重复键）

🔹 `multimap` 允许 **键重复**，同样基于红黑树实现，键有序。

| 操作     | 示例                 | 说明                           |
| -------- | -------------------- | ------------------------------ |
| 插入     | `mp.insert({x, y});` | 不支持 `mp[x] = y;` 语法       |
| 查找     | `mp.find(x);`        | 返回首次出现的迭代器           |
| 删除     | `mp.erase(x);`       | 删除所有键为 $x$ 的元素        |
| 统计     | `mp.count(x);`       | 返回键为 $x$ 的数量            |
| 二分查找 | `mp.lower_bound(x);` | 找到第一个键 $\geq x$ 的迭代器 |

---

### ✅ 九、总结对比表

| 容器类型        | 是否有序 | 键是否唯一 | 是否支持下标 | 平均查找复杂度            | 特点                   |
| --------------- | -------- | ---------- | ------------ | ------------------------- | ---------------------- |
| `map`           | ✅ 有序   | ✅ 唯一     | ✅ 支持       | \$O(\log n)\$             | 红黑树，键自动排序     |
| `unordered_map` | ❌ 无序   | ✅ 唯一     | ✅ 支持       | \$O(1)\$（最坏 \$O(n)\$） | 哈希表，速度快，但无序 |
| `multimap`      | ✅ 有序   | ❌ 可重复   | ❌ 不支持     | \$O(\log n)\$             | 支持重复键，按键有序   |

📌 根据实际需求选择合适的容器结构，权衡 **有序性**、**键唯一性** 和 **访问效率**。
