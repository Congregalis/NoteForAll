# 双指针
重要的是思想，优化 O(n^2) 到 O(n)

<details>
<summary>算法模板 (java)</summary>

```java
for (int i = 0, j = 0; i < n; i++) {

    while (j <= i && check(i, j)) j++;

    // 其他逻辑
    // ...
}
```

</details>

## 相关练手题目
- [Leetcode 3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)