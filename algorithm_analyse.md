# BFS算法框架

## 前言

BFS（广度优先级搜索）和DFS（深度优先级搜索）是比较常用的算法，其中DFS算是一种回溯算法，在二叉树中就相当于前序遍历算法。这里先分析BFS算法，为什么呢？因为BFS比较简单！废话少说，直接上分析！

## 算法分析

BFS相对于DFS最主要的区别在于：**BFS找到的路径一定是最短的，但是空间复杂度比DFS要大很多**。

BFS算法的核心思想实际上就是将问题抽象成“图”，从一个点开始，向周围扩散。一般来说，我们写BFS算法常用的数据结构是“队列”，每次都将一个节点周围的所有节点加入队列。

BFS算法是出现的场景**实际上就是让你在一幅“图”里面找出从“起点”到“终点”的最短距离**。把算法本质搞清楚之后，问题都能化归为基本问题。

如果上面的分析有点枯燥？不要紧，直接上栗子：

如走迷宫，有的格子周围的墙壁不能走，让你算从起点到终点的最短距离是多少？如果这个迷宫某些格子有“传送门”呢？

再比如有两个单词，要求通过替换某些字母，把其中一个变成另外一个，每次只能替换一个字母，最少要替换几次？

再比如连连看游戏，消除两个方块的条件不仅仅是图案相同，还要保证两个方块之间的最短连线不能多于两个两个拐点。你玩连连看，点击两个坐标，程序是如何找到最短连线的？如何判断最短连线有几个拐点？

。。。

其实，上面的例子无一不是一幅“图”，让你从起点走到终点，问最短路径，实则BFS本质。

## BFS算法框架

上面分析完了，下面直接上算法框架伪代码：

```C++
// 计算从起点 start 到终点 target 的最近距离
int BFS(Node start, Node target) {
    Queue<Node> q; // 核心数据结构
    Set<Node> visited; // 避免走回头路

    q.offer(start); // 将起点加入队列
    visited.add(start);
    int step = 0; // 记录扩散的步数

    while (q not empty) {
        int sz = q.size();
        /* 将当前队列中的所有节点向四周扩散 */
        for (int i = 0; i < sz; i++) {
            Node cur = q.poll();
            /* 划重点：这里判断是否到达终点 */
            if (cur is target)
                return step;
            /* 将 cur 的相邻节点加入队列 */
            for (Node x : cur.adj())
                if (x not in visited) {
                    q.offer(x);
                    visited.add(x);
                }
        }
        /* 划重点：更新步数在这里 */
        step++;
    }
}
```

记住：**因为BFS都是每次都是所有节点一起遍历，因此BFS总能比较快地找到最短路径。**



# 回溯算法框架

## 前言

熟悉二叉树的同学都知道，二叉树遍历有三种：前序遍历、中序遍历以及后序遍历

不熟悉也不打紧，下面给你简单科普一下：

前序遍历的遍历顺序是：**根节点-->左节点-->右节点**

中序遍历的遍历顺序是：**左节点-->根节点-->右节点**

后序遍历的遍历顺序是：**左节点-->右节点-->根节点**

代码如下：

```C++
/* 基本的二叉树节点 */
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int value) : val(value), left(nullptr), right(nullptr) {}
};

void NodeAction(TreeNode *node) {
    cout << node->val << endl;
}

// 前序遍历
void PreTraverse(TreeNode *root) {
    if (root == nullptr) {
        return;
    }
    NodeAction(root); // 当前节点需要做的操作，如：打印
    PreTraverse(root->left);
    PreTraverse(root->right);
}

// 中序遍历
void MiddleTraverse(TreeNode *root) {
    if (root == nullptr) {
        return;
    }
    MiddleTraverse(root->left);
    NodeAction(root); // 当前节点需要做的操作，如：打印
    MiddleTraverse(root->right);
}

// 后序遍历
void PostTraverse(TreeNode *root) {
    if (root == nullptr) {
        return;
    }
    PostTraverse(root->left);
    PostTraverse(root->right);
    NodeAction(root); // 当前节点需要做的操作，如：打印
}
```

从上面代码可以看出，二叉树的遍历核心思想是：递归、递归、递归！

## 算法分析

直接上干货，实际上回溯算法与二叉树遍历有异曲同工之妙！都是以递归为基础进行的，都是将一棵“树”完全遍历，也就是暴力穷举！但是回溯算法有个“做出选择”以及“撤销选择”的过程。

```C++
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return

    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

实际上回溯算法包含四大步骤就是：

- 路径列表
- 做出选择
- 撤销选择
- 终止条件

## N 皇后问题

简单解释一下：给你一个 N×N 的棋盘，让你放置 N 个皇后，使得它们不能互相攻击。

PS：皇后可以攻击同一行、同一列、左上左下右上右下四个方向的任意单位。

如果将一个回溯问题看作是一棵决策树，回溯算法就是在这棵树上从上往下一层一层地做出选择，那**N皇后**这个问题本质实际上就是每一层的决策树表示棋盘上的每一行；每个节点可以做出的选择是，在该行的任意一列放置一个皇后。

直接套公式：

```C++
vector<vector<string>> res;

// 是否可以在 board[row][col] 放置皇后？
bool isValid(vector<string>& board, int row, int col) {
    int n = board.size();
    // 检查列是否有皇后互相冲突
    for (int i = 0; i < n; i++) {
        if (board[i][col] == 'Q')
            return false;
    }
    // 检查右上方是否有皇后互相冲突
    for (int i = row - 1, j = col + 1;
         i >= 0 && j < n; i--, j++) {
        if (board[i][j] == 'Q')
            return false;
    }
    // 检查左上方是否有皇后互相冲突
    for (int i = row - 1, j = col - 1;
         i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] == 'Q')
            return false;
    }
    return true;
}

// 路径：board 中小于 row 的那些行都已经成功放置了皇后
// 选择列表：第 row 行的所有列都是放置皇后的选择
// 结束条件：row 超过 board 的最后一行
void backtrack(vector<string>& board, int row) {
    // 触发结束条件
    if (row == board.size()) {
        res.push_back(board);
        return;
    }

    int n = board[row].size();
    for (int col = 0; col < n; col++) {
        // 排除不合法选择
        if (!isValid(board, row, col))
            continue;
        // 做选择
        board[row][col] = 'Q';
        // 进入下一行决策
        backtrack(board, row + 1);
        // 撤销选择
        board[row][col] = '.';
    }
}

// 输入棋盘边长 n，返回所有合法的放置
vector<vector<string>> solveNQueens(int n) {
    // '.' 表示空，'Q' 表示皇后，初始化空棋盘。
    vector<string> board(n, string(n, '.'));
    backtrack(board, 0);
    return res;
}
```

从上面是不是能看得到二叉树遍历的身影？是就没错了！

回溯算法这种暴力遍历的方法导致的一个问题就是时间复杂度贼大。一般来说题目都会让你在适当的时候终止遍历，如上面的**N皇后**问题，一般来说，只需要你得出其中一种摆放方式即可。

将上面的代码添加适当的终止条件：

```c++
vector<vector<string>> res;

// 是否可以在 board[row][col] 放置皇后？
bool isValid(vector<string>& board, int row, int col) {
    int n = board.size();
    // 检查列是否有皇后互相冲突
    for (int i = 0; i < n; i++) {
        if (board[i][col] == 'Q')
            return false;
    }
    // 检查右上方是否有皇后互相冲突
    for (int i = row - 1, j = col + 1;
         i >= 0 && j < n; i--, j++) {
        if (board[i][j] == 'Q')
            return false;
    }
    // 检查左上方是否有皇后互相冲突
    for (int i = row - 1, j = col - 1;
         i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] == 'Q')
            return false;
    }
    return true;
}

// 路径：board 中小于 row 的那些行都已经成功放置了皇后
// 选择列表：第 row 行的所有列都是放置皇后的选择
// 结束条件：row 超过 board 的最后一行
bool backtrack(vector<string>& board, int row) {
    // 触发结束条件
    if (row == board.size()) {
        res.push_back(board);
        return true;
    }

    int n = board[row].size();
    for (int col = 0; col < n; col++) {
        // 排除不合法选择
        if (!isValid(board, row, col))
            continue;
        // 做选择
        board[row][col] = 'Q';
        // 进入下一行决策
        if (backtrack(board, row + 1)) {
            return true;
        }
        // 撤销选择
        board[row][col] = '.';
    }
    return false;
}

// 输入棋盘边长 n，返回所有合法的放置
vector<vector<string>> solveNQueens(int n) {
    // '.' 表示空，'Q' 表示皇后，初始化空棋盘。
    vector<string> board(n, string(n, '.'));
    backtrack(board, 0);
    return res;
}
```

总某种程度上来说，动态规划的一种暴力解就是回溯算法；一般来说单单只套用回溯算法的实用性题目是比较少的，都是需要在某个情况做出适当的调整~



# DFS算法框架

## 前言

前面说到了BFS，就少不了DFS了~

## 算法分析

DFS全称是深度优先搜索，听这个名字就知道，这个算法就是一直不断往下的“深度”搜索，思想就是：**一直往深处走，直到找到解或者走不下去为止**

欸欸欸，等等，怎么这么像之前说到的回溯算法？？？没错，你感觉是没错的，实际上这个DFS就是回溯算法！！！建议同学直接翻一翻我之前提到的回溯算法那篇博文。

```C++
void DFS(depth)
{
    if (找到解 || 走不下去了) {
        ...
        return;
    }
    // 列举下个情况
    DFS(depth + 1);
    // 没有下个情况了，回溯
    return;
}
```

这里就不多说了，直接上栗子：遍历二维坐标

```C++
const int32_t maxn = 100;
bool visted[maxn][maxn]; // 访问标记
int32_t positionRange[maxn][maxn]; // 坐标范围
int32_t direction[][2] = {0, 1, 0, -1, 1, 0, -1, 0}; // 方向向量，(x,y)周围的四个方向

// 边界条件和约束条件的判断
bool CheckEdge(int32_t x, int32_t y)
{
    // 满足条件
    if (!visted[x][y] && ...) {
        return true;
    } else {
        return false;
    }
}

void dfs(int32_t x,int32_t y)
{
    visted[x][y] = true; // 标记该节点被访问过
    // 出现目标态G
    if (positionRange[x][y] == G) {
        ... // 做相应处理
        return;
    }
    for (int32_t i = 0; i < 4; i++) {
        // 按照规则生成下一个节点，上面左右四个点
        if (CheckEdge(x + direction[i][0], y + direction[i][1])) {
            dfs(x + direction[i][0], y + direction[i][1]);
        }
    }
    return; // 没有下层搜索节点，回溯
}
```

这里扯一扯DFS与BFS的本质区别：

**DFS实际上就是实现类似于一个栈的操作，将节点按照深度优先的次序压栈，后面再以相反的次序出栈进行新的检测，实际上也就是递归实现之后的一个体现出来的特点。**

**BFS实际上就是实现一个队列的操作，将本节点处理完毕之后，再将周围相邻的节点入列，后面再依次遍历处理。**

