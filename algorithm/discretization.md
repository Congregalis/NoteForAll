# 离散化

当值域十分大，而内部元素的个数其实很小时（也就是很稀疏），可以使用这种思想。

其本质就是将很稀疏的数组映射到自然数上，从而节省时间空间。

<details>
<summary>核心代码 (java)</summary>

```java
List<Integer> alls = new ArrayList<>(); // 存储所有待离散化的值
Collections.sort(alls); // 排序
alls.subList(0, unique(alls)); // 去重，unique 函数下面给出。此外也可以使用 set 去重


// 通过二分找到 x 对应离散化后的值
public int find(List<Integer> alls, int x) {
    int l = 0, r = alls.size() - 1;
    while (l < r) {
        int mid = l + r >> 1;
        if (alls.get(mid) >= x) r = mid;
        else l = mid + 1;
    }

    return l + 1; // +1 是因为离散化后使用自然整数，如 1,2,3,...
}

// 去重 （前提是该 list 已经有序）
public int unique(List<Integer> alls) {
    int j = 0;
    for (int i = 0; i < alls.size(); i++) 
        if (i == 0 || alls.get(i) != alls.get(i-1))
            alls.set(j++, alls.get(i));
    
    return j; // 从 j 下标开始往后的都是重复了的无用元素
}

```

</details>