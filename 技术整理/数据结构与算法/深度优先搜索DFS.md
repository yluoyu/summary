
![](https://i.loli.net/2018/03/19/5aaf4c9f5e271.png)
广度优先搜索一层一层遍历，每一层遍历到的所有新节点，要用队列先存储起来以备下一层遍历的时候再遍历；而深度优先搜索在遍历到一个新节点时立马对新节点进行遍历：从节点 0 出发开始遍历，得到到新节点 6 时，立马对新节点 6 进行遍历，得到新节点 4；如此反复以这种方式遍历新节点，直到没有新节点了，此时返回。返回到根节点 0 的情况是，继续对根节点 0 进行遍历，得到新节点 2，然后继续以上步骤.
从一个节点出发，使用 DFS 对一个图进行遍历时，能够遍历到的节点都是从初始节点可达的，DFS 常用来求解这种 `可达性` 问题

在程序实现 DFS 时需要考虑以下问题:
- 栈: 用栈来保存当前节点信息，当遍历新节点返回时能够继续遍历当前节点。也可以使用递归栈
- 标记：和 BFS 一样同样需要对已经遍历过得节点进行标记





Leetcode : 547. Friend Circles (Medium)
Input:
[[1,1,0],
 [1,1,0],
 [0,0,1]]
Output: 2
Explanation:The 0th and 1st students are direct friends, so they are in a friend circle.
The 2nd student himself is in a friend circle. So return 2.
```
public int findCircleNum(int[][] M) {
    int n = M.length;
    int ret = 0;
    boolean[] hasFind = new boolean[n];
    for(int i = 0; i < n; i++) {
        if(!hasFind[i]) {
            dfs(M, i, hasFind);
            ret++;
        }
    }
    return ret;
}

private void dfs(int[][] M, int i, boolean[] hasFind) {
    hasFind[i] = true;
    int n = M.length;
    for(int k = 0; k < n; k++) {
        if(M[i][k] == 1 && !hasFind[k]) {
            dfs(M, k, hasFind);
        }
    }
}
```

#### 矩阵中的连通区域数量
Leetcode : 200. Number of Islands (Medium)
```
11110
11010
11000
00000
Answer: 1
```
```java
private int m, n;
private int[][] direction = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};

public int numIslands(char[][] grid) {
    if (grid == null || grid.length == 0) return 0;
    m = grid.length;
    n = grid[0].length;
    int ret = 0;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == '1') {
                dfs(grid, i, j);
                ret++;
            }
        }
    }
    return ret;
}

private void dfs(char[][] grid, int i, int j) {
    if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] == '0') return;
    grid[i][j] = '0';
    for (int k = 0; k < direction.length; k++) {
        dfs(grid, i + direction[k][0], j + direction[k][1]);
    }
}
```
