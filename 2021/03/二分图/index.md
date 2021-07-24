# 二分图


# 二分图

## 判图

- 利用染色法判

  ```cpp
  // 未试过
  bool dfs(int u, int c) {
      col[u] = c;
      for (int &v : gp[u]) {
          if (!col[v])  dfs(v, 3 - c);
          else if (col[v] == c) return false;
      }
      return true;
  }
  for (int i = 1; i <= n; ++i){
      if (col[i] == 0) dfs(i, 1);
  }
  ```

## 最大匹配

### 匈牙利算法

```cpp
int match[M];
bool vis[M];
bool dfs(int u) {
    for (int &v : gp[u]) {
        if (vis[v]) continue;
        vis[v] = true;
        if (!match[v] || dfs(match[v])) {
            match[u] = v, match[v] = u;
            return true;
        }
    }
    return false;
}
// 主函数里
fill_n(match, n + 1, 0); // n是点的个数
for (int i = 1; i <= n; ++i) {
    if (match[i]) continue;
    fill_n(vis, n + 1, false);
    dfs(i);
}
```


