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
- 利用的性质：`BST`的中序遍历是递增的。`pre`就是当前节点中序遍历中的前一个节点
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
                // 找左子树的最右节点，做如图所示的递归操作
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
---
### [230. Kth Smallest Element in a BST 中序遍历应用](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)
> Given a binary search tree, write a function `kthSmallest` to find the `k`th smallest element in it.
```c++
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        if (!root || k < 0){
            return 0;
        }
        stack<TreeNode*> s;
        TreeNode* cur = root;
        //TreeNode* pre = NULL;
        while(cur || !s.empty()){
            while(cur){
                s.push(cur);
                cur = cur->left;
            }
            if (!s.empty()){
                //TreeNode* tmp = s.top();
                cur = s.top();
                k--;
                s.pop();
                if (k==0){
                    return cur->val;
                }
                
                cur = cur->right;
            }
        }
        return 0;
    }
};
```
---
### [235. Lowest Common Ancestor of a Binary Search Tree 二叉搜索树最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)
> Given a binary search tree (BST), find the lowest common ancestor (LCA) of two given nodes in the BST.
> 
> According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”
- 题目给定是二叉搜索树，最近公共祖先就是`root->val`在中间，两边是`p->val`和`q->val`各在一个子树上（或者反之）
```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        while(root){
            if (root->val > p->val && root->val > q->val){
                root = root->left;
            }
            else if (root->val < p->val && root->val < q->val){
                root = root->right;
            }
            else{
                break;
            }
        }
        return root;
    }
};
```
---
### [236. Lowest Common Ancestor of a Binary Tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)
> Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.
> 
> According to the definition of LCA on Wikipedia: “The lowest common ancestor is defined between two nodes p and q as the lowest node in T that has both p and q as descendants (where we allow a node to be a descendant of itself).”
- 解法中心思想。最近公共祖先无外乎两种情况三个分支：
  - p，q在两边 -> 当前节点左/右子树各能找到一个
  - p，q在一边 -> 当前节点左/右子树能找到p和q，另一个子树找不到（NULL）
```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 中心思想：分别在左/右子树找p和q
        if(!root){
            return root;
        }
        else if (root->val == p->val || root->val == q->val){
            return root;
        }
        else{
            TreeNode* left_res = lowestCommonAncestor(root->left, p, q);
            TreeNode* right_res = lowestCommonAncestor(root->right, p, q);
            if (left_res && right_res){
                return root;
            }
            else if (!left_res){
                return right_res;
            }
            else{
            //if (!right_res){
                return left_res;
            }
        }
    }
};
```
---
## 树问题中的DFS

### [100. Same Tree](https://leetcode-cn.com/problems/same-tree/)
- 直接递归解题

---
### [113. Path Sum II](https://leetcode-cn.com/problems/path-sum-ii/)
> Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.
> 
> Note: A leaf is a node with no children.
- DFS中注意`push_back`和`pop`的位置
- 函数定义加`&`，引用传递
```c++
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        vector<vector<int>> res;
        vector<int> temp;
        helper(root, sum, res, temp);
        return res;
    }
    void helper(TreeNode* root, int sum, vector<vector<int>>& res, vector<int>& temp){
        if (!root){
            return;
        }
        temp.push_back(root->val);
        if (sum == root->val && !root->left && !root->right){
            res.push_back(temp);
        }
        
        helper(root->left, sum-root->val, res, temp);
        helper(root->right, sum-root->val, res, temp);
        temp.pop_back();
    }
};
```