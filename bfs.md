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