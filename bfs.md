# 广度优先搜索

### [102. Binary Tree Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

> Given a binary tree, return the level order traversal of its nodes' values. (ie, from left to right, level by level).
> 
> For example:
> 
> Given binary tree $[3,9,20,null,null,15,7]$,
> 
> return its level order traversal as:
> ```
> [
>   [3],
>   [9,20],
>   [15,7]
> ]
> ```

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (root == NULL){
            return res;
        }
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()){
            int size = q.size(); // 注意此处
            vector<int> level;
            for ( int i=0; i < size; i++){
                TreeNode* cur_node = q.front();
                q.pop();
                level.push_back(cur_node->val);
                if ( cur_node->left != NULL){
                    q.push(cur_node->left);
                }
                if ( cur_node->right != NULL){
                    q.push(cur_node->right);
                }
            }
            res.push_back(level);
        }
        return res;
    }
};
```
---
### [103. Binary Tree Zigzag Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)
> Given a binary tree, return the *zigzag* level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).
> 
> For example:
> 
> Given binary tree $[3,9,20,null,null,15,7]$,
> 
> return its level order traversal as:
> ```
> [
>   [3],
>   [20，9],
>   [15,7]
> ]
> ```
- 注意反向逻辑处理，不需要在入队顺序做文章，可以直接通过下标操作完成反向
```c++
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if (!root){
            return res;
        }
        queue<TreeNode*> q;
        bool order = true; // true：左到右 反之右到左
        q.push(root);
        while (!q.empty()){
            int size = q.size();
            vector<int> level(size);
            for(int i=0; i < size; i++){
                TreeNode* node = q.front();
                q.pop();
                if ( order){
                    level[i] = node->val;
                }
                else{
                    level[size-i-1] = node->val;
                }
                if ( node->left)
                    q.push(node->left);
                if ( node->right)
                    q.push(node->right);
                
            }
            res.push_back(level);
            order = !order;
        }
        return res;
    }
};
```
---
### [116. Populating Next Right Pointers in Each Node](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

>You are given a perfect binary tree where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

> Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.
>
>Initially, all next pointers are set to `NULL`.

```c++
class Solution {
public:
    Node* connect(Node* root) {
        if ( !root){
            return root;
        }
        queue<Node*> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            for ( int i=0; i < size; i++){
                Node* cur = q.front();
                q.pop();
                if (q.empty() || i == size-1){
                    cur->next = NULL;
                }
                else{
                    cur->next = q.front();
                }
                if (cur->left){
                    q.push(cur->left);
                }
                if ( cur->right){
                    q.push(cur->right);
                }
            } 
        }
        return root;
    }
};
```
---
### [127. Word Ladder(没太明白)](https://leetcode-cn.com/problems/word-ladder/)
> Given two words (beginWord and endWord), and a dictionary's word list, find the length of shortest transformation sequence from beginWord to endWord, such that:
> 
> Only one letter can be changed at a time.
> 
> Each transformed word must exist in the word list.
> 
> Note:
> 
> Return 0 if there is no such transformation sequence.
> - All words have the same length.
> - All words contain only lowercase alphabetic characters.
> - You may assume no duplicates in the word list.
> - You may assume beginWord and endWord are non-empty and are not the same.
```
Example 1:

Input:
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]

Output: 5

Explanation: As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.

```
- [参考资料](https://www.cnblogs.com/grandyang/p/4539768.html)
- 没太懂，题目要求shortest，但是求解过程没提
- > 明确了要用BFS，我们可以开始解题了，为了提到字典的查找效率，我们使用HashSet保存所有的单词。然后我们需要一个HashMap，来建立某条路径结尾单词和该路径长度之间的映射，并把起始单词映射为1。既然是BFS，我们需要一个队列queue，把起始单词排入队列中，开始队列的循环，取出队首词，然后对其每个位置上的字符，用26个字母进行替换，如果此时和结尾单词相同了，就可以返回取出词在哈希表中的值加一。如果替换词在字典中存在但在哈希表中不存在，则将替换词排入队列中，并在哈希表中的值映射为之前取出词加一。如果循环完成则返回0，参见代码如下：


```c++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> wordSet(wordList.begin(), wordList.end());
        if (wordSet.count(endWord) == 0){
            return 0;
        }
        unordered_map<string, int> wordMap; // 来建立某条路径结尾单词和该路径长度之间的映射
        wordMap[beginWord] = 1;
        queue<string> q;
        q.push(beginWord);
        while(!q.empty()){
            string word = q.front();
            q.pop();
            for( int i=0; i < word.size(); i++){
                string newWord = word;
                for ( char ch = 'a'; ch <= 'z'; ch++){
                    newWord[i] = ch;
                    if (wordSet.count(newWord) && newWord == endWord){
                        return 1+wordMap[word];
                    }
                    if (wordSet.count(newWord) && !wordMap.count(newWord)){
                        q.push(newWord);
                        wordMap[newWord] = 1 + wordMap[word];
                    }
                }
            }
        }
        return 0;
    }
};
```
---
### [207. Course Schedule 图+bfs](https://leetcode-cn.com/problems/course-schedule/)
```
There are a total of numCourses courses you have to take, 
labeled from 0 to numCourses-1.

Some courses may have prerequisites, 
for example to take course 0 you have to first take course 1, 
which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, 
is it possible for you to finish all courses?

Example 1:

Input: numCourses = 2, prerequisites = [[1,0]]
Output: true
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0. So it is possible.
Example 2:

Input: numCourses = 2, prerequisites = [[1,0],[0,1]]
Output: false
Explanation: There are a total of 2 courses to take. 
             To take course 1 you should have finished course 0, and to take course 0 you should
             also have finished course 1. So it is impossible.

```
- 见注释
```c++
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> graph(numCourses, vector<int>());
        vector<int> in(numCourses);
        for (auto a : prerequisites){
            // 建图
            in[a[0]] += 1; // 入度+1
            graph[a[1]].push_back(a[0]); // 有向图构建边
        }
        queue<int> q;
        for (int i=0; i < numCourses; i++){
            if (in[i] == 0){
                // 直接就能选的课
                q.push(i);
            }
        }
        while (!q.empty()){
            int cur = q.front();
            q.pop();
            for (int c : graph[cur]){
                in[c]--; // 入度-1
                if (in[c] == 0){
                    q.push(c); // 入度为0，能选这门课，加入队列
                }
            }
        }
        for (int i=0; i < numCourses; i++){
            if (in[i] != 0){
                return false;
            }
        }
        return true;
    }
};
```
---
### [301. Remove Invalid Parentheses](https://leetcode-cn.com/problems/remove-invalid-parentheses/)
> Remove the minimum number of invalid parentheses in order to make the input string valid. Return all possible results.
> 
> Note: The input string may contain letters other than the parentheses ( and ).
```
Example 1:
Input: "()())()"
Output: ["()()()", "(())()"]
```
- 见注释
```c++
class Solution {
public:
    vector<string> removeInvalidParentheses(string s) {
        vector<string> res;
        unordered_set<string> visited({s}); // hashset记录是否被处理过
        queue<string> q({s}); // bfs特征
        // 这个变量位置需要注意，题目限定删除尽可能少的字符
        // 定义在while外面代表‘到某一层能得到达标字符串就不再往下遍历了’
        bool found = false; 
        while (!q.empty()){
            string cur = q.front();
            q.pop();
            if (check(cur)){
                // 达标，加入res
                res.push_back(cur);
                found = true;
            }
            if (found){
                /*
                    此处不能是break，因为此题的bfs每层实际是‘删除了几个字符’
                    删除字符个数固定，解可能有多种
                */
                continue;
            }
            for (int i=0; i < cur.size(); i++){
                if (cur[i] != '(' && cur[i] != ')') continue;
                // trick：c++删除某一字符
                string new_s = cur.substr(0, i) + cur.substr(i+1); 
                if (!visited.count(new_s)){
                    visited.insert(new_s);
                    q.push(new_s); // bfs常规
                }
            }
        }
        return res;
    }
    bool check(string s){
        int cnt=0;
        for (char c : s){
            if (c!='(' && c!=')'){
                continue;
            }
            else if (c == '('){
                cnt += 1;
            }
            else if (c == ')'){
                cnt -= 1;
                if (cnt < 0){
                    return false;
                }
            }
        }
        return cnt == 0;
    }
};
```