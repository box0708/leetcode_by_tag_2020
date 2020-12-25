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
---
### [200. Number of Islands](https://leetcode-cn.com/problems/number-of-islands/)

> Given an $m * n$ 2d grid map of `'1'`s (land) and `'0'`s (water), return the number of islands.
> 
> An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

```c++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if (grid.size() == 0 || grid[0].size() == 0){
            return 0;
        }
        int res=0;
        for ( int i=0; i < grid.size(); i++){
            for ( int j=0; j < grid[0].size(); j++){
                if (grid[i][j] == '1'){
                    res += 1;
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }
    
    void dfs(vector<vector<char>>& grid, int i, int j){
        if ( i < 0 || j < 0 || i >= grid.size() || j >= grid[0].size() || grid[i][j] != '1'){
            return;
        }
        grid[i][j] = '*';
        dfs(grid, i-1, j);
        dfs(grid, i+1, j);
        dfs(grid, i, j-1);
        dfs(grid, i, j+1);
    }
};
```
---
### [329. Longest Increasing Path in a Matrix (记忆化搜索+DFS)](https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/)

> Given an integer matrix, find the length of the longest increasing path.
> 
> From each cell, you can either move to four directions: left, right, up or down. You may NOT move diagonally or move outside of the boundary (i.e. wrap-around is not allowed).
- 记忆化搜索+DFS
- 不要想复杂，`visited`作用可以被`dp`数组的值来指代
- 为什么不会转圈圈（为什么不需要visited数组？） 因为题意求递增，转圈圈问题不会发生
```c++
class Solution {
public:
    int longestIncreasingPath(vector<vector<int>>& matrix) {
        if ( matrix.empty() || matrix[0].empty()){
            return 0;
        }
        int m = matrix.size(), n = matrix[0].size();
        int result = INT_MIN;
        //vector<vector<bool>> visited(m, vector<n, false>);
        vector<vector<int>> dp(m, vector<int>(n, 0));
        for ( int i=0; i < m; i++){
            for ( int j=0; j < n; j++){
                result = max(result, dfs(matrix, dp, i, j, m, n));
            }
        }
        return result;
    }
    
    int dfs(vector<vector<int>>& matrix, vector<vector<int>>& dp, int i, int j, int m, int n){
        if (dp[i][j] > 0){
            return dp[i][j];
        }
        // 四个相邻位置分别判断
        if (i-1 >= 0 && i-1 < m && j >= 0 && j < n && matrix[i-1][j] > matrix[i][j]){
            dp[i][j] = max(dp[i][j], dfs(matrix, dp, i-1, j, m, n));
        }
        if (i+1 >= 0 && i+1 < m && j >= 0 && j < n && matrix[i+1][j] > matrix[i][j]){
            dp[i][j] = max(dp[i][j], dfs(matrix, dp, i+1, j, m, n));
        }
        if (i >= 0 && i < m && j-1 >= 0 && j-1 < n && matrix[i][j-1] > matrix[i][j]){
            dp[i][j] = max(dp[i][j], dfs(matrix, dp, i, j-1, m, n));
        }
        if (i >= 0 && i < m && j+1 >= 0 && j+1 < n && matrix[i][j+1] > matrix[i][j]){
            dp[i][j] = max(dp[i][j], dfs(matrix, dp, i, j+1, m, n));
        }
        dp[i][j] += 1;
        return dp[i][j];
    }
};
```
---
### [377. Combination Sum IV (记忆化搜索)](https://leetcode-cn.com/problems/combination-sum-iv/)
> Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.
> ```
> nums = [1, 2, 3]
> target = 4
>
>The possible combination ways are:
>(1, 1, 1, 1)
>(1, 1, 2)
>(1, 2, 1)
>(1, 3)
>(2, 1, 1)
>(2, 2)
>(3, 1)
>
>Note that different sequences are counted as different combinations.
>
>Therefore the output is 7.
>
> ```
- 记忆化搜索：`helper`函数中，先做边界处理；如果已经有`target`对应的值直接返回；如果没有进入计算
- DP不过，很奇怪，原因是大数相加溢出
- 外层遍历target，内层遍历num
```c++
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        unordered_map<int, int> mem;
        return helper(nums, target, mem);
    }
    int helper(vector<int>& nums, int target, unordered_map<int, int>& mem){
        if (nums.empty() || target < 0){
            return 0;
        }
        if (target == 0){
            return 1;
        }
        if (mem.count(target) == 1){
            return mem[target];
        }
        int res = 0;
        for ( int num : nums){
            res += helper(nums, target-num, mem);
        }
        mem[target] = res;
        return res;
    }
};
```
---
### [576. Out of Boundary Paths(还是记忆化搜索)](https://leetcode-cn.com/problems/out-of-boundary-paths/)

> There is an m by n grid with a ball. Given the start coordinate (i,j) of the ball, you can move the ball to adjacent cell or cross the grid boundary in four directions (up, down, left, right). However, you can at most move N times. Find out the number of paths to move the ball out of grid boundary. The answer may be very large, return it after mod $10^9 + 7$.


- （不一定准）看起来像dp但是解决起来比较麻烦的 考虑记忆化搜索
- `dp[move][x][y] > 0` 记忆化搜索特点：一个值只求一遍
- 边界条件的巧妙处理。此题第一个`if`就是避免了手动设置边界值，直接拿坐标来判断返回值


```c++
class Solution {
public:
    int findPaths(int m, int n, int N, int i, int j) {
        vector<vector<vector<uint>>> dp(N+1, vector<vector<uint>>(m, vector<uint>(n,0)));
        return dfs(dp, m, n, N, i, j) % 1000000007;
    }
    int dfs(vector<vector<vector<uint>>>& dp, int m, int n, int move, int x, int y){
        if ( x < 0 || x >= m || y < 0 || y >= n){
            return 1; // 已经走出去了
        }
        if ( move <= 0){
            return 0; // 没有步数
        }
        if ( x-move >= 0 && x+move < m && y-move >= 0 && y+move < n){
            return 0; // 肯定走不出去
        }
        // 记忆化搜索特点：某个值算出来了 就不再算了
        if ( dp[move][x][y] > 0){
            return dp[move][x][y];
        }
        int res = 0;
        res += dfs(dp, m, n, move-1, x-1, y);
        res %= 1000000007;
        res += dfs(dp, m, n, move-1, x+1, y);
        res %= 1000000007;
        res += dfs(dp, m, n, move-1, x, y-1);
        res %= 1000000007;
        res += dfs(dp, m, n, move-1, x, y+1);
        res %= 1000000007;
        dp[move][x][y] = res;
        return res;
    }
};
```