# 二分

## 整数二分

二分的本质并非单调性而是**两段性**，答案即是两段性的分界点。

<details>
<summary>算法模板 (java)</summary>

```java
boolean check(int x) {/* ... */} // 检查 x 是否满足某个性质

// 区间 [l, r] 被划分 [l, mid]，[mid+1, r] 时
int bSearch1(int l, int r) {
    while (l < r) {
        int mid = l + r >> 1;
        if (check(x)) r = mid;
        else l = mid + 1;
    }

    return l;
}

// 区间 [l, r] 被划分为 [l, mid-1], [mid, r] 时
int bSearch2(int l, int r) {
    while (l < r) {
        int mid = l + r + 1 >> 1; // 注意这里的 +1 是为了防止进入死循环
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
}

```

</details>

## 浮点数二分
浮点数二分只需要精度达到某范围即可。

<details>
<summary>算法模板 (java)</summary>

```java
boolean check(double x) {/* ... */} // 检查 x 是否满足某个性质

double bSearch3(double l, double r) {
    double eps = 1e-6; // eps 是精度，表示题目对精度的要求

    while (r - l > eps) {
        double mid = (l + r) / 2;
        if (check(x)) r = mid;
        else l = mid;
    }

    return l;
}


```

</details>