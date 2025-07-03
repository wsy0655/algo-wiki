

### 一、概述

`map` 是 C++ STL 中的有序键值对容器，底层实现为 **红黑树**。特点：

- 元素的 **键唯一**；
- 支持按键自动排序；
- 插入、删除、查找复杂度为 $O(\log n)$。

定义方式：

```cpp
map<key_type, value_type> mp;
```

**常见示例：**

```cpp
map<int, int> mp;                     // 键和值均为 int
map<string, int> mp;                 // 键为 string，值为 int
map<vector<int>, int> mp;           // 键为 vector<int>
map<int, vector<int>> mp;           // 值为 vector<int>
map<int, map<int, int>> mp;         // 二维 map
```

**📌 注意：** 键类型必须支持 $<$ 比较运算符。若使用自定义类型作键，需自定义比较函数。


### 二、基础操作

以下操作以 `map<int, int> mp` 为例。

| 操作    | 示例             | 说明                | 复杂度           |
| ----- | -------------- | ----------------- | ------------- |
| 插入/修改 | `mp[x] = y;`   | 若 `x` 不存在则插入，否则修改 | $O(\log n)$ |
| 查找键   | `mp.count(x)`  | 存在返回 1，不存在返回 0    | $O(\log n)$ |
| 删除键   | `mp.erase(x);` | 删除键为 `x` 的元素      | $O(\log n)$ |
| 获取键数  | `mp.size();`   | 返回键的数量            | $O(1)$      |
| 清空    | `mp.clear();`  | 清空所有元素            | $O(n)$      |


### 三、遍历方式


**方法一：传统迭代器写法**

```cpp
for (map<int, int>::iterator it = mp.begin(); it != mp.end(); ++it) 
{
    cout << it->first << " " << it->second << "\n";
}
```

- `it->first`：键
- `it->second`：值

**方法二：使用`auto`**


```cpp
for (auto it : mp) 
{
    cout << it.first << " " << it.second << "\n";
}
```


### 四、常见应用场景


**例 1：统计整数频次（元素较小）**

```cpp
int cnt[100005];
for (int i = 1; i <= n; i++)
{
    int x;
    cin >> x;
    cnt[x]++;
}
```

- 适用于 $1 \leq x \leq 10^7$，使用数组即可。主要是空间不超限制。

**例 2：统计整数频次（元素较大）**

```
map<int, int> cnt;
for (int i = 1; i <= n; i++)
{
    int x;
    cin >> x;
    cnt[x]++;
}
```

适用于 $x$ 的范围远超 $10^5$（如 $1 \leq x \leq 10^9$ 或更大）。

- 主打 **随用随创建**，内存和创建个数有关。

**例 3：统计字符串频次**


```cpp
map<string, int> cnt;
for (int i = 1; i <= n; i++)
{
    string s;
    cin >> s;
    cnt[s]++;
}
```


### 五、算法函数（利用键有序的特性）

以 `map<int, int> mp` 为例。


- `lower_bound(x)`：返回第一个大于等于 $x$ 的键的迭代器。


```cpp
auto it = mp.lower_bound(x);
if (it != mp.end()) 
{
    cout << it->first << " " << it->second;
}
```


- `upper_bound(x)`：返回第一个大于 $x$ 的键的迭代器。

```cpp
auto it = mp.upper_bound(x);
if (it != mp.end()) 
{
    cout << it->first << " " << it->second;
}
```

### 六、二维 map


**方法一：数组嵌套 map**

```cpp
map<int, int> mp[100005];
mp[i][j] = k;
```
含义：第 $i$ 个 map 中，键 $j$ 的值为 $k$。


**方法二：map 嵌套 map**


```cpp
map<int, map<int, int>> mp;
mp[i][j] = k;
```
含义：键为 $i$，值是一个 map，其中键为 $j$ 的值为 $k$。

### 七、unordered_map


`unordered_map` 是 `map` 的一种无序版本，其底层实现是哈希表。


- 键无序
- 平均时间复杂度为 $O(1)$，最坏为 $O(n)$（哈希冲突）

```cpp
unordered_map<int, int> ump;
ump[x] = y;
```


支持与 `map` 相同的函数：

- `ump[x] = y;` 创建或修改
- `ump.count(x);` 判断键是否存在
- `ump.erase(x);` 删除键
- `ump.size();` 获取键数
- `ump.clear();` 清空容器

**❗ 注意：** 不支持 lower_bound() 和 upper_bound()，因为键无序。


### 八、multimap

- `multimap` 允许键重复

- 底层也是红黑树，键有序

- 操作复杂度仍为 $O(\log n)$


| 操作 | 示例                   | 说明                  |
| -- | -------------------- | ------------------- |
| 插入 | `mp.insert({x, y});` | 不支持 `mp[x] = y;` 语法 |
| 查找 | `mp.find(x);`        | 返回第一次出现的迭代器         |
| 删除 | `mp.erase(x);`       | 删除所有键为 `x` 的元素      |
| 统计 | `mp.count(x);`       | 返回键为 `x` 的个数        |
| 二分 | `mp.lower_bound(x);` | 第一个键 ≥ x 的位置        |


### 九、总结

| 容器类型            | 是否有序 | 是否唯一键 | 支持下标访问 | 平均复杂度（查找）             |
| --------------- | ---- | ----- | ------ | --------------------- |
| `map`           | ✅ 有序 | ✅ 唯一键 | ✅ 支持   | $O(\log n)$         |
| `unordered_map` | ❌ 无序 | ✅ 唯一键 | ✅ 支持   | $O(1)$（最坏 $O(n)$） |
| `multimap`      | ✅ 有序 | ❌ 可重复 | ❌ 不支持  | $O(\log n)$         |




