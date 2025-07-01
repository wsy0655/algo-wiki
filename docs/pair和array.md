## <center>pair<center/>

* `pair` 是一个容器，可以存储两个不同类型的值。例如，`pair<int, string> a` 可以存储一个整数和一个字符串。可以简单理解为系统自带的两个元素的结构体。
* 获取元素一：`a.first`，获取元素二：`a.second`。
* `pair` 已经实现了默认的比较方式，即两个 `pair` 比较先按照 `first` 优先，`first` 相同比 `second`。
* 因此 `pair` 类型可以直接使用 `sort` 函数。（当需要按照 first 优先比较的时候）

```cpp
pair<int, int> a[N];

for (int i = 1; i <= n; i++)
{
    cin >> a[i].first >> a[i].second;
}

sort(a + 1, a + n + 1); // 直接按 first 优先从小到大排序

sort(a + 1, a + n + 1, greater<>()); // 按照 first 优先从大到小排序
```

## <center>array<center/>

* `array` 是一个数组容器。例如，`array<int, 3> a` 相当于一个大小为 $3$ 的 `int` 数组，其元素分别为 $a[0],\ a[1],\ a[2]$。
* `array` 已经实现了默认的比较方式，即两个 `array` 比较先按照下标为 $0$ 的优先，下标为 $0$ 的相同的继续向后比，以此类推。
* `array` 比 `pair` 更适用于多个元素的情况，但 `array` 要求所有元素类型必须一致。

```cpp
array<int, 3> a[N];
for (int i = 1; i <= n; i++)
{
    cin >> a[i][0] >> a[i][1] >> a[i][2];
}

sort(a + 1, a + n + 1); 

sort(a + 1, a + n + 1, greater<>());
```

## <center>总结<center/>

是否有自带的支持超过 $2$ 个以上元素且元素类型可以不同的容器？

答：`tuple`（元组），感兴趣可以自学。或者选择 `pair` 套 `pair`。例 `pair<int, pair<char, bool>>`