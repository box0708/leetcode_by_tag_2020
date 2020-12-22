# 深度优先遍历（DFS）

[参考资料](https://leetcode-cn.com/circle/article/YLb5l4/https://leetcode-cn.com/circle/article/YLb5l4/)

---
### [79. Word Search](https://leetcode-cn.com/problems/word-search/)

> Given an $m * n$ board and a word, find if the word exists in the grid.
> 
> The word can be constructed from letters of sequentially adjacent cells, where "adjacent" cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.
- 基础题
```c++
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        if (board.size() == 0 || board[0].size() == 0){
            return false;
        }
        for ( int i=0; i < board.size(); i++){
            for ( int j=0; j < board[0].size(); j++){
                if (dfs(board, i, j, 0, word)){
                    return true;
                }
            }
        }
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, int i, int j, int pos, string& word){
        // 边界检查
        if (i >= board.size() || j >= board[0].size() || i < 0 || j < 0 || pos >= word.size() || word[pos] != board[i][j]){
            return false;
        }
        if (pos == word.size() -1 && word[pos] == board[i][j] ){
            return true;
        }
        char temp = board[i][j];
        /*
            此处为了省一个变量（开一个m*n的二维bool数组也可以）
            题目限定只有大小写英文字母，可以这么操作
        */
        board[i][j] = '*'; 
        bool result =      dfs(board, i-1, j, pos+1, word)
                        || dfs(board, i+1, j, pos+1, word)
                        || dfs(board, i, j-1, pos+1, word)
                        || dfs(board, i, j+1, pos+1, word);
        board[i][j] = temp; // 注意把值赋回去
        return result;
    }
};
```
---
### [130. Surrounded Regions](https://leetcode-cn.com/problems/surrounded-regions/)
> Given a 2D board containing `'X'` and `'O'` (the letter O), capture all regions surrounded by `'X'`.
> 
> A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region.
- 最初思路错了
- 先把不需要改的都弄成特殊字符，然后遍历改回来
- coding细节
```c++
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        if (board.size() == 0 || board[0].size() == 0){
            return;
        }
        // step1: 把所有从边界延伸出来的连续O变成*
        for ( int i=0; i < board.size(); i++){
            for ( int j=0; j < board[0].size(); j++){
                if ( (i==0 || j==0 || i == board.size()-1 || j == board[0].size()-1) 
                        && board[i][j] == 'O'){
                    helper(board, i, j);
                }
            }
        }
        // step2：把剩下的O变X，*变O
        for ( int i=0; i < board.size(); i++){
            for ( int j=0; j < board[0].size(); j++){
                if ( board[i][j] == 'O'){
                    board[i][j] = 'X';
                }
                if ( board[i][j] == '*'){
                    board[i][j] = 'O';
                }
            }
        }
    }
    
    void helper(vector<vector<char>>& board , int i, int j){
        if (i < 0 || i >= board.size() || j < 0 || j >= board[0].size() || board[i][j] != 'O'){
            return;
        }
        board[i][j] = '*';
        helper(board, i-1, j);
        helper(board, i+1, j);
        helper(board, i, j-1);
        helper(board, i, j+1);
    }
};
```