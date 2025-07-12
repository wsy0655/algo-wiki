# 优先队列 ⚡

优先队列 `priority_queue` 是一种 **特殊的队列**，它能够保证每次取出的元素都是队列中优先级最高的。在 C++ 中，优先队列通常使用 **二叉堆** 实现。


## 基本概念 📚

`priority_queue<Type, Container, Compare>`，其中 Type 为存储的数据类型，Container 为容器类型，Compare 为比较函数。

**参数说明**

- Type：存储的数据类型，必须能够进行大小比较。
- Container：容器类型，默认为 `vector`。
- Compare：比较函数，默认为 `less<Type>`，即 **大根堆**。

**常见定义**

```cpp
priority_queue<int> q; // 默认大根堆
priority_queue<int, vector<int>, greater<int>> q; // 小根堆
```

**常用函数**


| 函数名 | 功能 | 复杂度| 备注 |
| :---: | :---: | :---: | :---: |
| `push(x)` | 插入元素 `x` | $O(\log n)$ | |
| `top()` | 返回堆顶元素 | $O(1)$ | 确保堆不为空 |
| `pop()` | 删除堆顶元素 | $O(\log n)$ | 确保堆不为空|
| `empty()` | 判断堆是否为空 | $O(1)$ | |
| `size()` | 返回堆的大小 | $O(1)$ | |

---

## 自定义类型的优先队列 🛠️

当自定义类型（结构体）后，使用优先队列。需要 **重载比较运算符** 使得优先队列按照自己的方式比较。

本节介绍常用的几种方式，请看下文！

---

### 运算符重载简介


运算符重载（Operator Overloading）让自定义类型像内置类型一样：

- 支持算术运算（$\text{+},\text{-},\text{*}, \text{/}$ 等）。
- 支持比较运算（$\text{<}, \text{>}$ 等）

**重载格式**

```cpp
// 返回类型 operator 运算符(参数列表) 
{ 
    ... 
}
```

**加法运算符重载示例**

```cpp
struct Point 
{
    int x, y;
    // 重载加法运算符
    Point operator+(const Point& p) 
    {
        Point c;
        c.x = x + p.x;
        c.y = y + p.y;
        return c;
    }
};
int main()
{
    Point p1 = {1, 2}, p2 = {3, 4};
    Point p3 = p1 + p2; // p3 = (4, 6)
    return ;
}
```


```cpp
struct Point 
{
    int x, y;
};
Point operator+(const Point& a, const Point& b) 
{
    Point c;
    c.x = a.x + b.x;
    c.y = a.y + b.y;
    return c;
}
int main()
{
    Point p1 = {1, 2}, p2 = {3, 4};
    Point p3 = p1 + p2; // p3 = (4, 6)
    return ;
}
```

---


**小于运算符重载示例**

```cpp
struct Point 
{
    int x, y;
    bool operator<(const Point& it) const 
    {
        return x < it.x;
    }
} a[N];
int main()
{
    for (int i = 1; i <= n; i++) cin >> a[i].x >> a[i].y;
    sort(a + 1, a + n + 1); // 按照x从小到大排序
}
```

- 在这段代码中，`const` 关键字用于确保对象不会被修改。具体来说，这两个 `const` 的作用如下：

- 第一个 `const`（在参数列表中）：`const Point& it`
     - 这个 `const` 修饰的是参数 `it`，表示传入的 `Point` 对象 `it` 是一个常量引用。这意味着在函数体内不能修改 `it` 对象的成员变量 `x` 和 `y`。使用常量引用可以避免不必要的对象拷贝，同时保证传入的对象不会被修改。

- 第二个 `const`（在函数末尾）：`const`（在 `bool operator<(const Point& it) const ` 中）
     - 这个 `const` 修饰的是成员函数 `operator<`，表示该成员函数是一个常量成员函数。这意味着该函数不会修改所属类的任何成员变量。常量成员函数通常用于不改变对象状态的操作。

总结：

- 第一个 `const` 保证传入的参数 `p` 不会被修改。
- 第二个 `const` 保证成员函数 `operator<` 不会修改所属类的任何成员变量。



---



### 自定义类型优先队列  🛠️



#### 写法一 🧩


- 自定义第三个参数 $\text{Compare}$
    -   $\text{priority\_queue}$ 的第三个模板参数必须是 **仿函数** 类型，不能直接传入函数指针。
    -   自定义比较逻辑，决定 **堆顶** 元素的优先级


**小根堆示例**

```cpp
// 自定义类型
struct Node 
{
    int d, id;
};

// 仿函数：重载 operator()
struct Cmp 
{
    bool operator()(const Node& a, const Node& b) const 
    {
        // 返回 true 则 a “优先级” 低于 b
        return a.d > b.d;  // 小根堆：d 大者沉底
    }
};

// 使用方式
priority_queue<Node, vector<Node>, Cmp> pq;
```


**原理解析与常见疑问**

- 为何重载 $\text{operator()}？$
    - $\text{priority\_queue}$ 内部需要一个 **可调用对象**，接受两个参数并返回 $\text{bool}$，以决定元素的顺序。
    - 结构体重载 $\text{operator()}$ 后即可充当这种 **比较仿函数**。
- 为何使用 **大于号**？
    - 比较仿函数返回 $\text{true}$ 表示 **左侧优先级更低**，排到队列后面。 
    - 小根堆希望 **值小的优先出**，故写 $a.d > b.d$。  大的元素被认为 **优先级低**。
    -  大根堆反过来：$a.d < b.d$。
  


---
  

#### 写法二 ⚙️


- 重载 **小于运算符** $\text{operator<}$
    - 优先队列默认使用 **小根堆**，重载小于运算符即可实现自定义类型的大根堆。


**小根堆示例**

```cpp
struct Node 
{
    int d, id;
    bool operator<(const Node& it) const 
    {
        return d > it.d;  // 小根堆：d 大者沉底
    }
};

// 使用方式
priority_queue<Node> pq;
```


---

#### 写法三 🚀


用 $\text{pair}$ 或 $\text{array}$ 免重载


**示例代码**

```cpp
using pii = pair<int,int>;
using pli = pair<long long,int>;
// 小根堆，按 first 升序：
priority_queue<pii, vector<pii>, greater<pii>> pq1;
// 大根堆（按 first 降序）：
priority_queue<pii> pq2;

// 同理：array<int,2>
using ary = array<int,2>;
priority_queue<ary, vector<ary>, greater<ary>> pq3;
```


---


## 与 `set` 的对比 🔄

| 特性               | 优先队列 `priority_queue` | 集合 `set`/`multiset` |
|--------------------|---------------------------|-----------------------|
| 元素顺序           | 仅保证堆顶元素优先级最高  | 所有元素有序          |
| 查找任意元素       | 不支持                    | $O(\log n)$           |
| 删除任意元素       | 不支持                    | $O(\log n)$           |
| 插入复杂度         | $O(\log n)$               | $O(\log n)$           |
| 取出最值复杂度     | $O(\log n)$               | $O(\log n)$           |
| 底层实现           | 二叉堆                    | 红黑树                |

**适用场景**：

- 优先队列：仅需频繁访问最值元素，不关心其他元素顺序。
- 集合：需要维护元素的全序关系，支持任意元素的查找和删除。


若仅需维护最大/最小值，且不需要查找或删除任意元素，$\text{priority\_queue}$ 更轻量；常数更低。 
