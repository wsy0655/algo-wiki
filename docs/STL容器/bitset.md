## 📌bitset


### 🧠 简述

- $\texttt{bitset}$ 是 C++ 标准库中的高效位容器，用于存储固定大小的二进制数据（$0/1$）
- 提供接近硬件级别的位操作性能，特别适合状态压缩和布尔运算

**存储原理**

- 内存按字节寻址，普通 $\texttt{bool}$ 类型每个值占用 $1$ 字节（$8$ 比特）
- $\texttt{bitset}$ 通过位压缩技术，每个比特存储一个 $0/1$ 值



**空间效率**

存储单个 $0/1$ 值仅需 $1$ 位空间

- 相比 $\texttt{int}$ 类型（$32$ 位/值），空间利用率提升 $32$ 倍
- 相比 $\texttt{bool}$ 数组（$8$ 位/值），空间利用率提升 $8$ 倍


**性能优势**

- 单条机器指令可同时处理 $32/64$ 位数据（位级并行）
- 批量操作（如移位、位运算）时间复杂度为 $O(n/w)$
  - $w$ = 机器字长（$32$ 位或 $64$ 位）
  - 理论加速比最高可达 $w$ 倍







### 📘 创建和初始化

$\texttt{bitset}$ 的大小是在定义时指定的，且大小不可修改。

可以通过字符串或数字来初始化 $\texttt{bitset}$。

```cpp
bitset<8> b1;           // 默认初始化为 00000000
bitset<8> b2("10101010"); // 从二进制字符串初始化
bitset<8> b3(10);        // 从整数初始化，10 的二进制表示为 00001010
```

**输出：**

```cpp
cout << b1 << endl; // 输出: 00000000
cout << b2 << endl; // 输出: 10101010
cout << b3 << endl; // 输出: 00001010
```


**支持的运算符**

例如定义 $\texttt{bitset<8> b2("10101010")}$。


- $\texttt{[]}$: 访问其特定的一位。例：$\texttt{cout << b[3]}$。
- $\texttt{==}$ 和 $\texttt{!=}$：比较两个 $\texttt{bitset}$ 内容是否完全一样。
- 位运算。$\texttt{bitset}$ 只能与 $\texttt{bitset}$ 进行位运算，若要和整型进行位运算，要先将整型转换为 $\texttt{bitset}$。

此外，$\texttt{bitset}$ 还提供了 `C++` 流式 `IO` 的支持，这意味着你可以通过 $\texttt{cin/cout}$ 进行输入输出。


```cpp
bitset<8> bs;
cin >> bs; // 输入必须是 01 字符串，且长度和 bitset 大小匹配。
cout << bs;
int num;
cin >> num;
bitset<32> bs(num); // 将整数转换为 32 位 bitset
```


### ⚙️ 常见函数


$\texttt{bitset}$ 提供了许多内置函数用于操作和查询二进制数据。

- $\texttt{count()}$：返回 $\texttt{bitset}$ 中 1 的个数。
- $\texttt{size()}$：返回 $\texttt{bitset}$ 的大小。
- $\texttt{any()}$：如果 $\texttt{bitset}$ 中存在 $1$，则返回 `true`。
- $\texttt{none()}$：如果 $\texttt{bitset}$ 中所有位都为 $0$，返回 `true`。
- $\texttt{all()}$：如果 $\texttt{bitset}$ 中所有位都为 $1$，返回 `true`。
- $\texttt{flip()}$：反转所有位。$\texttt{b.flip(pos)}$：翻转某一位。
- $\texttt{set()}$：将所有位设为 $1$。$\texttt{b.set(pos, true/false)}$：设置某一位的值。
- $\texttt{reset()}$：将所有位设为 $0$。$\texttt{b.reset(pos)}$：设置某一位为 `false`。
- $\texttt{to\_string()}$：将 $\texttt{bitset}$ 转换为二进制字符串。
- $\texttt{to\_ulong()}$：返回转换成的 $\texttt{unsigned long}$ 表达。
- $\texttt{to\_ullong()}$：返回转换成的 $\texttt{unsigned long long}$ 表达。




```cpp
bitset<8> b("10101010");
cout << b.count() << endl;  // 输出: 4 (1 的数量)
cout << b.size() << endl; // 输出：8
cout << b.any() << endl;  // 输出: 1 (存在 1)
cout << b.none() << endl; // 输出: 0 (存在 1)
cout << b.all() << endl; // 输出：0（存在 0）
b.flip(); // b.flip(5)
b.set(); // b.set(5, 1)
b.reset(); // b.reset(5)
```

**🧩 常用函数**

| 函数              | 说明                      | 示例              |
| --------------- | ----------------------- | --------------- |
| `count()`       | 返回 1 的数量                | `b.count()`     |
| `size()`        | 返回总位数                   | `b.size()`      |
| `any()`         | 是否至少有一位为 1              | `b.any()`       |
| `none()`        | 是否全为 0                  | `b.none()`      |
| `all()`         | 是否全为 1                  | `b.all()`       |
| `flip()`        | 翻转所有位                   | `b.flip()`      |
| `flip(pos)`     | 翻转第 `pos` 位             | `b.flip(3)`     |
| `set()`         | 所有位设为 1                 | `b.set()`       |
| `set(pos, val)` | 设置第 `pos` 位为 `val`      | `b.set(2, 0)`   |
| `reset()`       | 所有位设为 0                 | `b.reset()`     |
| `reset(pos)`    | 第 `pos` 位设为 0           | `b.reset(2)`    |
| `to_string()`   | 转为字符串（如 `"0101..."`）    | `b.to_string()` |
| `to_ulong()`    | 转为 `unsigned long`      | `b.to_ulong()`  |
| `to_ullong()`   | 转为 `unsigned long long` | `b.to_ullong()` |



**转为其他类型**

```cpp
bitset<4> b(5);
unsigned long ul = b.to_ulong();
unsigned long long ull = b.to_ullong();
string s = b.to_string();  // 转为字符串
```

### ✅ 位运算


支持与、或、异或、非等位运算，可以高效地进行多个二进制数的运算。

两个长度为 $n$ 的 `bitset` 做位运算的时间复杂度为 $O(\frac{n}{w})$。


```cpp
bitset<8> a("10101010");
bitset<8> b("11001100");

bitset<8> c = a & b;  // 按位与
bitset<8> d  = a | b;  // 按位或
bitset<8> e = a ^ b;  // 按位异或
bitset<8> f = ~a;     // 按位非

cout << c << endl;  // 输出: 10001000
cout << d << endl;   // 输出: 11101110
cout << e << endl;  // 输出: 01100110
cout << f << endl;  // 输出: 01010101
cout << (a << 3) << endl; // 输出：01010000
cout << (a >> 3) << endl; // 输出：00010101
```



