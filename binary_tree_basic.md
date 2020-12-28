# 二叉树相关

## 遍历写法

### [94. Binary Tree Inorder Traversal 中序非递归遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)
```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        if (!root){
            return res;
        }
        stack<TreeNode*> s;
        TreeNode* cur = root;
        while(cur != NULL || !s.empty()){
            // cur指针非空或栈不空，继续整个流程
            while(cur){
                // 左孩子不断压栈，直到指到最左边
                s.push(cur);
                cur = cur->left;
            }
            if(!s.empty()){
                // 栈非空，弹出栈顶，存元素，处理当前节点右孩子
                cur = s.top();
                res.push_back(cur->val);
                s.pop();
                cur = cur->right;
            }
        }
        return res;
    }
};
```