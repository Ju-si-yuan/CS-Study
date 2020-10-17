#### [52. N皇后 II](https://leetcode-cn.com/problems/n-queens-ii/)

# 题目描述

n皇后问题研究的是如何将 *n* 个皇后放置在 *n*×*n* 的棋盘上，并且使皇后彼此之间不能相互攻击。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/8-queens.png)

上图为 8 皇后问题的一种解法。

给定一个整数 *n*，返回 *n* 皇后不同的解决方案的数量。

# 示例

```
输入: 4
输出: 2
解释: 4 皇后问题存在如下两个不同的解法。
[
 [".Q..",  // 解法 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // 解法 2
  "Q...",
  "...Q",
  ".Q.."]
]
```

# 题解

与[51. N 皇后](https://leetcode-cn.com/problems/n-queens/)一样，唯一不同的地方在于，一个返回的是具体的解，而本题是返回可行解的个数

采用回溯解法，用vector\<string>board表示棋盘，row和col分别表示行和列，从第一行开始放置皇后，在放置之前，首先判断此处可不可以放置，用函数isValid(board,row,col)来判断，函数isValid需要判断在col列上有没有皇后，左上方有没有皇后，右上方有没有皇后。

# 代码

```c++
class Solution {
public:
    int totalNQueens(int n) {
        //未放置皇后的棋盘
        vector<string> board(n,string(n,'.'));
        int ans=0;
        //从第0行开始回溯
        backtrack(board,0,ans);
        return ans;
    }
    void backtrack(vector<string> &board,int row,int &ans){
        int n=board.size();
        //已经找到一个可行解
        if(row==n){
            ++ans;
            return;
        }
        //从第一列开始，寻找在第row行可以放置皇后的位置
        for(int col=0;col<n;++col){
            //如果board[row][col]可以放置皇后
            if(isValid(board,row,col)){
                board[row][col]='Q';
                //从下一行继续回溯
                backtrack(board,row+1,ans);
                board[row][col]='.';
            }
        }
    }
    bool isValid(vector<string> &board,int row,int col){
        int n=board.size();
        //要在board[row][col]上放置皇后，row行上肯定没有，所以需要判断col列上有没有皇后
        for(int i=0;i<row;++i){
            if(board[i][col]=='Q')
                return false;
        }
        //左上
        for(int i=row-1,j=col-1;i>=0&&j>=0;--i,--j){
            if(board[i][j]=='Q')
                return false;
        }
        //右上
        for(int i=row-1,j=col+1;i>=0&&j<n;--i,++j){
            if(board[i][j]=='Q')
                return false;
        }
        return true;
    }
};
```

