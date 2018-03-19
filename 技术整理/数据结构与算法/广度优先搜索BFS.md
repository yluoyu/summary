图是一种灵活的数据结构，一般作为一种模型用来定义对象之间的关系或联系。对象由顶点（V）表示，而对象之间的关系或者关联则通过图的边（E）来表示。
图可以分为有向图和无向图，一般用G=(V,E)来表示图。经常用邻接矩阵或者邻接表来描述一副图。

在图的基本算法中，最初需要接触的就是图的遍历算法，根据访问节点的顺序，可分为广度优先搜索（BFS）和深度优先搜索（DFS）

BFS
![](https://i.loli.net/2018/03/19/5aaf558d74c32.png)
广度优先搜索的搜索过程有点像一层一层地进行遍历：从节点 0 出发，遍历到 6、2、1 和 5 这四个新节点。

继续从 6 开始遍历，得到节点 4 ；从 2 开始遍历，没有下一个节点；从 1 开始遍历，没有下一个节点；从 5 开始遍历，得到 3 和 4 节点。这一轮总共得到两个新节点：4 和 3 。

反复从新节点出发进行上述的遍历操作。

可以看到，每一轮遍历的节点都与根节点路径长度相同。设 di 表示第 i 个节点与根节点的路径长度，推导出一个结论：对于先遍历的节点 i 与后遍历的节点 j，有 di<=dj。利用这个结论，可以求解最短路径 `最优解` 问题：第一次遍历到目的节点，其所经过的路径为最短路径，如果继续遍历，之后再遍历到目的节点，所经过的路径就不是最短路径。
在程序实现 BFS 时需要考虑以下问题：
- 队列：用来存储每一轮遍历的节点
- 标记：对于遍历过得节点，应该将它标记，防止重复遍历；

计算在网格中从原点到特定点的最短路径长度
```
[[1,1,0,1],
[1,0,1,0],
[1,1,1,1],
[1,0,1,1]]
```
```java
public int minPathLength(int[][] grids, int tr, int tc) {
    int[][] next = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    int m = grids.length, n = grids[0].length;
    Queue<Position> queue = new LinkedList<>();
    queue.add(new Position(0, 0, 1));
    while (!queue.isEmpty()) {
        Position pos = queue.poll();
        for (int i = 0; i < 4; i++) {
            Position nextPos = new Position(pos.r + next[i][0], pos.c + next[i][1], pos.length + 1);
            if (nextPos.r < 0 || nextPos.r >= m || nextPos.c < 0 || nextPos.c >= n) continue;
            if (grids[nextPos.r][nextPos.c] != 1) continue;
            grids[nextPos.r][nextPos.c] = 0;
            if (nextPos.r == tr && nextPos.c == tc) return nextPos.length;
            queue.add(nextPos);
        }
    }
    return -1;
}

private class Position {
    int r, c, length;
    public Position(int r, int c, int length) {
        this.r = r;
        this.c = c;
        this.length = length;
    }
}
```
