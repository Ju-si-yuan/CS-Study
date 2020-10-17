####  [51. N 皇后](https://leetcode-cn.com/problems/n-queens/)

# 题目描述

n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/8-queens.png)

上图为 8 皇后问题的一种解法。

给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。

每一种解法包含一个明确的 n 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。

# 示例

```
输入：4
输出：[
 [".Q..",  // 解法 1
  "...Q",
  "Q...",
  "..Q."],
   ["..Q.",  // 解法 2
  "Q...",
  "...Q",
  ".Q.."]
]
解释: 4 皇后问题存在两个不同的解法。
```

# 题解

方向一的斜线：同一条斜线上的每个位置满足：**行下标与列下标之差相等**

方向二的斜线：同一条斜线上的每个位置满足：**行下标与列下标之和相等**

具体步骤：

1. 需要定义一个全局的棋盘状态，因为题目要求返回vector<vector<string>>,所以定义棋盘状态为：
   `vector<string> board(n, string(n, '.'));`
   即所有的位置初始化为不放置皇后。

2. 定义回溯函数（即深度优先搜索的递归函数）
   `void backtrack(vector<string>& board, int row, vector<vector<string>>& res)`
   首先我们需要传入棋盘状态 board, 我们需要不断的修改这个状态，也需要用这个状态来判断某位置是否可以放置皇后
   然后，因为我们是一行一行搜索的，所以需要有参数row表示当前是哪一行。
   最后，我们传入最终返回的结果集合的引用 vector<vector<string>>& res

3. 成功退出条件

   ```c++
       if(row==board.size()){
           res.emplace_back(board);
           return;
       }
   
   ```

4. 核心搜索过程（剪枝+回溯）

   ```c++
   for(int col=0; col<n; ++col){
       if(!isValid(board, row, col)){
           continue;
       }
   
       board[row][col] = 'Q';
       backtrack(board, row+1, res);
       board[row][col] = '.';
   }
   ```

   

# 代码



```c++
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> res; 
        vector<string> board(n, string(n, '.'));
        backtrack(board, 0, res);
        return res;
    }

    void backtrack(vector<string>& board, int row, vector<vector<string>>& res){
        int n = board.size();
        if(row==n){
            res.emplace_back(board);
            return;
        }
        for(int col=0; col<n; ++col){
            if(!isValid(board, row, col)){
                continue;
            }

            board[row][col] = 'Q';
            backtrack(board, row+1, res);
            board[row][col] = '.';
        }
    }

    bool isValid(vector<string>& board, int row, int col){
        int n = board.size();
        //检查同列
        for(int i=0; i<row; i++){
            if(board[i][col]=='Q'){
                return false;
            }
        }
        //右上
        for(int i=row-1, j=col+1; i>=0 && j<n; i--, j++){
            if(board[i][j]=='Q'){
                return false;
            }
        }
        //左上
        for(int i=row-1, j=col-1; i>=0 && j>=0; i--, j--){
            if(board[i][j]=='Q'){
                return false;
            }
        }
        return true;
    }
};
```



