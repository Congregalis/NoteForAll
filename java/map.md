# Map



## 遍历 Map 的几种方式
> 一句话总结：懒人使用 **forEach**

以一个实例来说明：
```java
Map<String, Integer> map = new HashMap<String, Integer>() {
    {
        put("a", 1);
        put("b", 2);
        put("c", 3);
    }
};
```

- 遍历**键**
```java
for (String s : map.keySet()) {
    System.out.print(s + " ");
}
```

- 遍历**值**
```java
for (Integer value : map.values()) {
    System.out.println(value + " ");
}
```

- 遍历**键+值**
1. 使用 **for**
```java
for (Map.Entry<String, Integer> e : map.entrySet())
    System.out.println(e.getKey() + " " + e.getValue());
```

2. 使用**迭代器**

```java
Iterator<Map.Entry<String, Integer>> entryIterator = map.entrySet().iterator();
while (entryIterator.hasNext()) {
    Map.Entry<String, Integer> next = entryIterator.next();
    System.out.println(next.getKey() + " " + next.getValue());
}
```

这两种方式其实是一样的。**迭代器**的好处是可以在迭代的过程中**动态地删除元素**，如
```java
Iterator<Map.Entry<String, Integer>> entryIterator = map.entrySet().iterator();
while (entryIterator.hasNext()) {
    Map.Entry<String, Integer> next = entryIterator.next();
    if(next.getKey().equals("b")) entryIterator.remove();
    else System.out.println(next.getKey() + " " + next.getValue());
}
```

**jdk8 之后**又有了更方便的遍历方式给我们这些懒人使用，如

1. 使用 **lambda** 表达式
```java
map.forEach((key, value) -> {
    System.out.println(key + " " + value);
});
```
2. **stream** 单线程遍历
```java
map.entrySet().stream().forEach((stringIntegerEntry -> {
    System.out.println(stringIntegerEntry.getKey() + " " + stringIntegerEntry.getValue());
}));
```
3. stream **多线程**遍历
```java
map.entrySet().stream().parallel().forEach(entry -> {
    System.out.println(entry.getKey() + " " + entry.getValue());
});
```

由于多线程遍历是并发执行的，因此**效率**会比前面的高，但同时也无法保证元素的**执行顺序**。