## pair

* `pair` 是一个容器，可以存储两个**不同类型**的值。例如：

```cpp
pair<int, string> a = {1, "hello"};
```

* 可以简单理解为系统自带的两个元素的结构体。
* 获取元素：`a.first` 获取第一个值，`a.second` 获取第二个值。
* `pair` 实现了默认的比较方式，两个 `pair` 会先比较 `first`，若相同则比较 `second`，因此可以直接用于排序。


___

**示例：排序 pair 数组**

```cpp
pair<int, int> a[N];
for (int i = 1; i <= n; i++) 
{
    cin >> a[i].first >> a[i].second;
}

sort(a + 1, a + n + 1);            // 按 first 从小到大排序
sort(a + 1, a + n + 1, greater<>()); // 按 first 从大到小排序
```


**pair 的嵌套**


```cpp
pair<int, pair<string, bool>> a = {1, {"hello", true}};
cout << a.first << "\n";
cout << a.second.first << " " << a.second.second;
```

---

## array

* `array` 是 STL 中的 **定长数组容器**，定义方式为：

```cpp
array<int, 3> a = {1, 2, 3};
```

* 相当于一个固定长度为 $3$ 的 `int` 数组，元素访问方式为 `a[0], a[1], a[2]`。
* `array` 的元素类型必须**完全一致**。
* `array` 支持默认比较，优先比较第 $0$ 位，依次往后。

___



**示例：排序 array 数组**

```cpp
array<int, 3> a[N];
for (int i = 1; i <= n; i++) 
{
    cin >> a[i][0] >> a[i][1] >> a[i][2];
}

sort(a + 1, a + n + 1);             // 默认升序排序
sort(a + 1, a + n + 1, greater<>()); // 默认降序排序
```

---

## tuple

* `tuple`（元组）是 STL 中支持多个不同类型元素的容器。
* 与 `pair` 类似，但可以存储 $2$ 个以上元素。
* 定义方式：

```cpp
tuple<int, string, double> t = {1, "abc", 3.14};
```

* 获取元素使用 `get<下标>(t)`，下标从 $0$ 开始：

```cpp
cout << get<0>(t) << get<1>(t) << get<2>(t);
```

* `tuple` 也支持比较与排序（从前往后逐个比较），可以直接 sort。

**示例：排序 tuple 数组**

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

## 总结对比

| 容器类型  | 元素数量 | 是否可不同类型 | 支持比较排序 | 特点              |
| ----- | ---- | ------- | ------ | --------------- |
| `pair`  | $2$    | ✅       | ✅      | STL 中用于两个值的组合   |
| `array` | 固定   | ❌       | ✅      | 所有元素类型一致，支持随机访问 |
| `tuple` | 任意   | ✅       | ✅      | 更通用的多元素容器       |

✅ 若你需要存储多个不同类型的数据，并且数量超过 $2$，推荐使用 `tuple`。
✅ 若元素数量为 $2$，`pair` 简洁高效。
✅ 若元素类型一致，且固定长度，可选择 `array`。
