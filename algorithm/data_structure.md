# 常用数据结构

这里用**数组**的方式实现以下各个常用数据结构

## 单链表

含以下操作：
- `addToHead` : 往头结点后面插入结点 （头插法）
- `add` : 往某个结点后面插入结点
- `delete` : 删除某结点

实现时最好结合画图更好理解
<details>
<summary>实现模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
// e: 结点 i 的值
// ne: 结点 i 的 next 值
// head: 头结点指向哪个下标
// idx: 当前用到了哪个点
static int[] e = new int[N], ne = new int[N];
static int head = -1, idx = 0;

public static void addToHead(int x) {
    e[idx] = x;
    ne[idx] = head;
    head = idx;
    idx ++;
}

public static void add(int k, int x) {
    e[idx] = x;
    ne[idx] = ne[k];
    ne[k] = idx;
    idx++;
}

public static void delete(int k) {
    ne[k] = ne[ne[k]];
}
```

</details>

## 双链表

含以下操作：
- `addToRight` : 往某结点的右侧插入结点
- `addToLeft` : 不用单独实现，只需对该节点的左边结点做 `addToRight` 操作
- `delete` : 删除某结点

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开

// e: 结点 i 的值
// l: 结点 i 的 left 值 (即左侧结点)
// r: 结点 i 的 right 值 (即右侧结点)
// head: 头结点指向哪个下标
// idx: 当前用到了哪个点
static int[] e = new int[N], l = new int[N], r = new int[N];
static int idx;

public static void init() {
    // 注：这里idx = 0 为左端点， idx = 1 为右端点, 所以 idx 从 2 开始
    r[0] = 1;
    l[1] = 0;
    idx = 2;
}

// 在节点 k 的右侧插入
public static void insertToRight(int k, int x) {
    e[idx] = x;
    r[idx] = r[k];
    l[idx] = k;
    l[r[k]] = idx;
    r[k] = idx;
    idx++;
}

// 删除节点 k
public static void delete(int k) {
    l[r[k]] = l[k];
    r[l[k]] = r[k];
}
```

</details>

## 栈

先进后出的数据结构，只能从栈顶放入元素并从栈顶弹出元素

含以下操作：
- `push` : 将元素推入栈中 （放入栈顶）
- `pop` : 将栈顶元素出栈 
- `isEmpty` : 判断栈是否为空
- `peak` : 查看栈顶元素

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] stk = new int[N]; // 栈底层的数组
static int tt = 0; // 指向栈顶的指针，这里以 tt = 1 开始表示栈中有元素

public static void push(int x) {
    stk[++tt] = x;
}

public static void pop() {
    tt--;
}

public static boolean isEmpty() {
    return tt <= 0;
}

public static int peak() {
    return stk[tt];
}
```

</details>

## 队列

先进先出的数据结构，只能从队尾放入元素并从队头拿出元素

含以下操作：
- `push` : 将元素加入队列中 （放入队尾）
- `pop` : 将队头元素出队 
- `isEmpty` : 判断队列是否为空
- `peak` : 查看队头元素

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] q = new int[N]; // 队列底层的数组
static int hh = 0, tt = -1; // hh 代表指向队头元素，tt 代表指向队尾元素 （tt 从 0 开始表示队列有元素）

public static void push(int x) {
    q[++tt] = x;
}

public static int pop() {
    return (hh++);
}

public static boolean isEmpty () {
    return hh > tt;
}

public static int peak() {
    return q[hh];
}
```

</details>


## 单调栈

快速找到某个元素左边（或右边）离它最近的比它大（小）的元素

以下以找到数组中每个元素**左边第一个比他小的**元素为例，则需要维护一个单调递增的栈，具体原因可以看代码中的注释。
<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] q = new int[N]; // 栈底层的数组，模拟栈
static int tt = -1; // 指向栈顶的指针，这里以 tt = 1 开始表示栈中有元素）

// 遍历 a 数组
for (int i = 0; i < n; i++) {
    int x = a[i];
    
    // 若当前元素比栈顶元素小或相等则弹出栈顶元素
    // 因为 当前元素 比起 栈顶元素 而言，比栈顶元素离 x 近且又比它小，栈顶元素对之后的元素就无用了
    while (tt >= 0 && q[tt] >= x) tt--; 
    
    if (tt < 0) System.out.print(-1 + " "); // 若栈中无元素，返回 -1
    else System.out.print(q[tt] + " ");
    
    q[++tt] = x;
}
```

</details>

## 单调队列

可以模拟滑动窗口，统计窗口中的最大/最小值或者用二分查找窗口中的某个值。

以下以查找数组中以 k 为窗口大小，输出每个窗口的最小值为例。这则需要维护一个单调递增的队列，保证最小值始终在队列的头部。

<details>
<summary>算法模板 (java)</summary>

```java
static int N = 100010; // 开的数组大小，根据需求去开
    
static int[] q = new int[N]; // 队列底层的数组
static int hh = 0, tt = -1; // hh 代表指向队头元素，tt 代表指向队尾元素 （tt 从 0 开始表示队列有元素）
        
hh = 0; tt = -1;
for (int i = 0; i < n; i++) {
    // 若队列大于窗口大小，则从头部开始出队直到符合窗口大小
    if (hh <= tt && i - k + 1 > q[hh]) hh++;
    

    // 从队尾开始淘汰比 当前元素a[i] 大的元素，维护一个单调增队列
    while (hh <= tt && a[q[tt]] >= a[i]) tt--;
    q[++tt] = i;
    
    if (i >= k - 1) wr.write(a[q[hh]] + " "); // 单调增队列最小值在队头
}
```

</details>


## Trie

Trie树可以用来高效地插入和查找字符串。

<details>
<summary>算法模板 (java 数组实现)</summary>

```java
static int N = 100010;
static int[][] son = new int[N][26];
static int[] cnt = new int[N];
static int idx = 0;

public static void insert(String word) {
    int p = 0;
    
    for (char c : word.toCharArray()) {
        int u = c -'a';
        if (son[p][u] == 0) son[p][u] = ++idx;
        p = son[p][u];
    }
    
    cnt[p]++;
}

public static int query(String word) {
    int p = 0;
    
    for (char c : word.toCharArray()) {
        int u = c - 'a';
        if (son[p][u] == 0) return 0;
        p = son[p][u];
    }
    
    return cnt[p];
}
```

</details>

<details>
<summary>算法模板 (java 内部类实现)</summary>

```java
static class Node {
    Node[] children = new Node[26];
    int cnt = 0;
}

public void insert(Node root, String word) {
    Node p = root;
    
    for (char c : word.toCharArray()) {
        int u = c -'a';
        if (p.children[u] == null) p.children[u] = new Node();
        p = p.children[u];
    }
    
    p.cnt++;
}

public int query(Node root, String word) {
    Node p = root;
    
    for (char c : word.toCharArray()) {
        int u = c - 'a';
        if (p.children[u] == null) return 0;
        p = p.children[u];
    }
    
    return p.cnt;
}
```

</details>

## 并查集

可以以近乎 O(1) 的时间将
1. 两个集合合并
2. 查找两个元素是否在一个集合中

原理：每个集合用一棵树表示，树根即整个集合的编号（代表），每个结点存储它的父结点，以 `p[x]` 代表 `x` 的父结点

每个结点调用 `find()` 便可以迅速的找到该结点所在的树的树根（代表）编号。额外地，使用**路径压缩**来进一步降低时间复杂度。

**路径压缩**：即让每个结点的父节点直接变成所在树的根节点，具体可以看 `find()` 实现

`find()`
```java
public static int find(int x) {
    if (p[x] != x) p[x] = find(p[x]);
    return p[x];
}
```

1. 如何判断是否为树根？

    `if (p[x] == x)`

2. 如何求 x 的集合编号

    `find(x)`

3. 如何合并两集合

    如合并 x 与 y 两个结点所在的集合
    ```java
    int px = find(x), py = find(y);
    p[px] = py;

4. 如何判断两个结点是否在一个集合中

    `if (find(x) == find(y))`

### 带权并查集

使用额外的数组保存一些**额外信息**，如集合中总共有多少元素等。

例题：
- [食物链](https://www.acwing.com/problem/content/242/)