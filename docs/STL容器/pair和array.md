## 🧩 pair（对组）

`pair` 是一个 STL 中的容器，可以存储两个 **不同类型** 的值。

```cpp
pair<int, string> a = {1, "hello"};
```

🔸 可以简单理解为系统自带的两个元素的结构体。
🔸 获取元素方式：

```cpp
a.first      // 获取第一个值
a.second     // 获取第二个值
```

🔸 支持默认比较：先比较 `first`，若相同则比较 `second`，因此可直接用于排序。

### ✨ 示例：排序 pair 数组

```cpp
pair<int, int> a[N];
for (int i = 1; i <= n; i++) 
{
    cin >> a[i].first >> a[i].second;
}

sort(a + 1, a + n + 1);              // 按 first 从小到大排序
sort(a + 1, a + n + 1, greater<>()); // 按 first 从大到小排序
```

### 🔁 pair 的嵌套

```cpp
pair<int, pair<string, bool>> a = {1, {"hello", true}};
cout << a.first << "\n";
cout << a.second.first << " " << a.second.second;
```

---

## 📦 array（定长数组容器）

`array` 是 STL 中的 **固定长度数组容器**，所有元素类型必须一致。

```cpp
array<int, 3> a = {1, 2, 3};
```

🔸 相当于长度为 `3` 的 `int` 数组
🔸 访问方式：`a[0], a[1], a[2]`
🔸 支持默认比较：从第 `0` 位开始依次比较

### ✨ 示例：排序 array 数组

```cpp
array<int, 3> a[N];
for (int i = 1; i <= n; i++) 
{
    cin >> a[i][0] >> a[i][1] >> a[i][2];
}

sort(a + 1, a + n + 1);              // 默认升序排序
sort(a + 1, a + n + 1, greater<>()); // 默认降序排序
```

---

## 📚 tuple（元组）

`tuple` 是 STL 中的多元素容器，支持存储多个 **不同类型** 的数据。

```cpp
tuple<int, string, double> t = {1, "abc", 3.14};
```

🔸 类似 `pair`，但可存储两个以上的值
🔸 元素访问使用 `get<下标>(t)`，下标从 `0` 开始：

```cpp
cout << get<0>(t) << get<1>(t) << get<2>(t);
```

🔸 支持默认比较与排序（逐元素从前往后比较）

### ✨ 示例：排序 tuple 数组

```cpp
tuple<int, int, int> a[N];
for (int i = 1; i <= n; i++) 
{
    int x, y, z;
    cin >> x >> y >> z;
    a[i] = {x, y, z};
}

sort(a + 1, a + n + 1); // 默认升序排序
```

---

## 🧮 总结对比表

| 容器类型 | 元素数量 | 是否支持不同类型 | 支持排序 | 特点                           |
| -------- | -------- | ---------------- | -------- | ------------------------------ |
| `pair`   | 2        | ✅                | ✅        | 简洁高效的双元素容器           |
| `array`  | 固定长度 | ❌                | ✅        | 所有元素类型一致，支持随机访问 |
| `tuple`  | 任意个数 | ✅                | ✅        | 更通用的多元素组合，结构更复杂 |

✅ 推荐使用选择：

* 若需要存储多个不同类型的数据，且数量超过 2 ➤ **使用 `tuple`**
* 若只有两个值需组合 ➤ **使用 `pair` 简洁高效**
* 若固定长度且元素类型一致 ➤ **选择 `array` 更实用**
