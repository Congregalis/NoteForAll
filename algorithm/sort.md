# 排序算法

## 快速排序
基于分治。
1. 找一个分界点 pivot，可以是 q[l]、q[l+r>>1] 或 q[r]
2. **调整区间**，小于 pivot 的放在左区间，大于 pivot 的放在右区间
3. 递归处理左右两段

<details>
<summary>算法模板 (java)</summary>

```java
public static void quickSort(int[] q, int l, int r) {
    if (l >= r) return;

    int x = q[l], i = l - 1, j = r + 1;

    while (i < j) {


        while (q[++i] < x);
        while (q[--j] > x);

        if (i < j) swap(i, j);
    }

    quickSort(l, j);
    quickSort(j + 1, r);
}
```

</details>


## 归并排序
基于分治。
1. 确定分界点 mid = l + r >> 1
2. 递归排序左、右区间
3. 将两段区间**归并**在一块


<details>
<summary>算法模板 (java)</summary>

```java
public static void mergeSort(int[] q, int l, int r) {
    if (l >= r) return;

    int mid = l + r >> 1;

    mergeSort(q, l, mid);
    mergeSort(q, mid + 1, r);

    int k = 0, i = l, j = mid + 1;
    while (i <= mid && j <= r) {
        if (q[i] <= q[j]) tmp[k++] = q[i++];
        else tmp[k++] = q[j++];
    }
    while (i <= mid) tmp[k++] = q[i++];
    while (j <= r) tmp[k++] = q[j++];

    for (i = l, j = 0; i <= r; i++, j++) q[i] = tmp[j];
}
```

</details>