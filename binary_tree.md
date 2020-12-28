# 二叉树题目
---
### [98. Validate Binary Search Tree 中序遍历应用](https://leetcode-cn.com/problems/validate-binary-search-tree/)
> Given the `root` of a binary tree, determine if it is a valid binary search tree (BST).
> 
> A valid BST is defined as follows:
>
> -The left subtree of a node contains only nodes with keys less than the node's key.
> 
> -The right subtree of a node contains only nodes with keys greater than the node's key.
> 
> -Both the left and right subtrees must also be binary search trees.

```c++
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        /*
            此题需要pre指针。没太理解，但是遍历过程中pre和cur指针实际就是在
            检查当前节点和当前节点左孩子是否符合BST的要求
        */
        if (!root){
            return true;
        }
        stack<TreeNode*> s;
        TreeNode* cur = root;
        TreeNode* pre = NULL;
        while(cur || !s.empty()){
            while(cur){
                s.push(cur);
                cur = cur->left;
            }
            if (!s.empty()){
                cur = s.top();
                s.pop();
                if (pre && pre->val >= cur->val){
                    return false;
                }
                pre = cur;
                cur = cur->right;
            }
        }
        return true;
    }
};
```
---
### [114. Flatten Binary Tree to Linked List](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)
> Given a binary tree, flatten it to a linked list in-place.
- 题解看图
```
     1
    / \
   2   5
  / \   \
 3   4   6

   1
    \
     2
    / \
   3   4
        \
         5
          \
           6
           
   1
    \
     2
      \
       3
        \
         4
          \
           5
            \
             6
```
```c++
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode* cur = root;
        while(cur){
            if (cur->left){
                // 找左子树的最右节点
                TreeNode* tmp = cur->left;
                while (tmp->right){
                    tmp = tmp->right;
                }
                tmp->right = cur->right;
                cur->right = cur->left;
                cur->left = NULL;
            }
            cur = cur->right;
        }
    }
};
```