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
