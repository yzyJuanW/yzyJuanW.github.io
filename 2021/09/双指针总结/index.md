# 双指针总结


## 能用双指针的条件

- 具有区间递增性，即随着区间的两边扩散，某个性质递增增长

## 常用套路与技巧

- 板子一

```cpp
for(int l = 0, r = 0; r < n; ++r) {
    add(arr[r]); // 添加右指针
    while (isbad()) {
        remove(arr[l]);
        l += 1;
    }
    if (isgood()) { // 必要的话价格判断判断
    	update(ans); // 更新ans
    }
}
```

- 板子二

```cpp
for (int l = 0, r = 0; r < n; ++r) {
    add(arr[r]);
    while (isgood()) {
        update(ans);
        remove(arr[l]);
        l += 1;
    }
}
```

- 技巧：当删除不好维护时，考虑用两个栈来维护，即建两个栈 `sl, sr`， 当添加时往 `sr` 上添加，当删除时，如果 `sl` 为空，则将 `sr` 上的所有元素倒入 `sl` 后再删除 `sl` 的栈顶元素，若 `sl` 不为空，则直接删除 `sl` 的栈顶元素
- 未完待续……
