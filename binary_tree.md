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
---
### [124. Binary Tree Maximum Path Sum 比较精妙](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)
> Given a **non-empty** binary tree, find the maximum path sum.
> 
> For this problem, a path is defined as any node sequence from some starting node to any node in the tree along the parent-child connections. The path must contain at least one node and does not need to go through the root.
- 最大路径和三种可能：
  - 以某节点为头 + 左子树上选一个路径
  - 。。。。 + 右子树上选一个路径
  - 穿过该节点
- 详见备注
```c++
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        if (!root){
            return 0;
        }
        int res = INT_MIN;
        int tmp = helper(root, res);
        return res;
    }
    
    int helper(TreeNode* root, int& res){
        /*
            含义：以root为首的路径的最大和（可以理解为局部最大值）
            res为引用，全局最大值
        */
        if (!root){
            return 0;
        }
        // 此处和0求max，相当于对返回值剪枝，如果加出负数，加0就相当于剪掉
        int left = max(helper(root->left, res), 0);
        int right = max(helper(root->right, res), 0);
        // 疑问：穿过root节点的路径一定最大嘛？
        // 上一个备注解答了疑问
        res = max(res, left + right + root->val);
        
        return max(left, right) + root->val;
    }
};
```
---
### [437. Path Sum III 树+回溯](https://leetcode-cn.com/problems/path-sum-iii/)
> You are given a binary tree in which each node contains an integer value.
> 
> Find the number of paths that sum to a given value.
> 
> The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).
> 
> The tree has no more than 1,000 nodes and the values are in the range -1,000,000 to 1,000,000.
- 路径，考虑回溯，`path`存的是根节点到当前节点的路径节点，`curSum`存的是从根节点到目前节点的累加和（从顶到下）
- 题意要求中间某节点开始的路径也算，所以`for`循环从头开始删节点
```c++
class Solution {
public:
    int pathSum(TreeNode* root, int sum) {
        if (!root){
            return 0;
        }
        int res = 0;
        int curSum = 0;
        vector<TreeNode*> path;
        helper(root, sum, curSum, path, res);
        return res;
    }
    void helper(TreeNode* root, int sum, int curSum, vector<TreeNode*>& path, int& res){
        if (!root){
            return;
        }
        curSum += root->val;
        path.push_back(root);
        if (curSum == sum){
            res += 1; // 从顶到当前节点路径和满足要求
        }
        int tmpSum = curSum;
        for (int i=0; i < path.size()-1; i++){
            // 从头开始删节点，看是否中间某节点开始有和等于sum的路径
            tmpSum -= path[i]->val;
            if (tmpSum == sum){
                res += 1;
            }
        }
        helper(root->left, sum, curSum, path, res);
        helper(root->right, sum, curSum, path, res);
        path.pop_back();
    }
};
```
---
### [剑指 Offer 26. 树的子结构](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)
> 输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)
> 
> B是A的子结构， 即 A中有出现和B相同的结构和节点值。
> ```
> 例如:
> 给定的树 A:
> 
>      3
>     / \
>    4   5
>   / \
>  1   2
> 给定的树 B：
> 
>    4 
>   /
>  1
> 返回 true，因为 B 与 A 的一个子树拥有相同的结构和节点值。
> ```
- 本题`helper`函数类似判断两棵树是否相同的逻辑，但是需要定制
```c++
class Solution {
public:
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if (!A || !B){
            // 题干要求，空树为false
            return false;
        }
        else if ( A->val == B->val && helper(A->left, B->left) && helper(A->right, B->right)){
            // 当前节点匹配上，则检查左右子树是否符合子结构
            return true;
        }
        // 当前节点失配，在A的左右树找“子结构的根节点”
        return isSubStructure(A->left, B) || isSubStructure(A->right, B);
    }
    bool helper(TreeNode* A, TreeNode* B){
        if (!B){
            // B树到头了，匹配成功
            return true;
        }
        if (!A || (A->val != B->val)){
            // A树到头了，B树还有节点，false
            return false;
        }
        else{
            // 递归检查左右子树
            return helper(A->left, B->left) && helper(A->right, B->right);
        }
    }
};
```
---
### [剑指 Offer 33. 二叉搜索树的后序遍历序列](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)
> 输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 `true`，否则返回 `false`。假设输入的数组的任意两个数字都互不相同。
- 后序遍历特点：左孩子 右孩子 根节点
- 二叉搜索树特点：左子树任一节点 < 根节点 < 右子树任一节点
- 递归思路：分别找根节点的左孩子和右孩子，达标后检查左子树和右子树是否达标
- 第二个`while`改成`for`循环能加快运行速度，剪枝
```c++
class Solution {
public:
    bool verifyPostorder(vector<int>& postorder) {
        if (postorder.empty()){
            return true;
        }
        return helper(postorder, 0, postorder.size()-1);
    }
    bool helper(vector<int>& arr, int start, int end){
        if (start >= end){
            return true;
        }
        int index = start;
        while (arr[index] < arr[end]){ // 不能是<= 否则会直接走到end下标
            index ++;
        }
        int pos = index; // [start, index-1]为左子树所有节点，[index, end-1]为右子树所有节点
        while (arr[index] > arr[end]){
            index ++; // 右子树是否都大于根节点
        }
        if (index != end){
            return false; // 右子树发现比根节点小的，false
        }
        return helper(arr, start, index-1) && helper(arr, index, end-1);
    }
};
```
---
## 树+链表

### [109. Convert Sorted List to Binary Search Tree](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)
> Given the head of a singly linked list where elements are **sorted in ascending order**, convert it to a height balanced BST.
> 
> For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.
> ```
> Input: head = [-10,-3,0,5,9]
> Output: [0,-3,9,-10,null,5]
> Explanation: One possible answer is [0,-3,9,-10,null,5], which represents the shown > height balanced BST.
> ```
- 快慢指针找链表中点，然后递归
- coding细节：`if (head != slow)`
```c++
class Solution {
public:
    TreeNode* sortedListToBST(ListNode* head) {
        if (!head){
            return NULL;
        }
        if (!head->next){
            return new TreeNode(head->val);
        }
        ListNode* slow=head, *fast=head, *last=head;
        while(fast->next && fast->next->next){
            last = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        // slow为链表中点
        TreeNode* cur_head = new TreeNode(slow->val);
        fast = slow->next;
        //slow->next = NULL;
        last->next = NULL;
        if (head != slow){
            cur_head->left = sortedListToBST(head);
        }
        cur_head->right = sortedListToBST(fast);
        return cur_head;
    }
};
```
---
## 树的重新构建

### [105. Construct Binary Tree from Preorder and Inorder Traversal 前序中序重建二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)
```c++
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (preorder.empty() || inorder.empty()){
            return NULL;
        }
        return helper(preorder, inorder, 0, preorder.size()-1, 0, inorder.size()-1);
    }
    TreeNode* helper(vector<int>& preorder, vector<int>& inorder, int pstart, int pend, int instart, int inend){
        // 递归调用，找根节点，分左右子树
        if (pstart > pend || instart > inend){
            return NULL;
        }
        int cur_root_value = preorder[pstart];
        int pos_inorder = instart;
        for ( ; pos_inorder <= inend; pos_inorder++){
            if (inorder[pos_inorder] == cur_root_value){
                break; // 当前根节点在中序遍历中的下标
            }
        }
        TreeNode* cur_root = new TreeNode(cur_root_value);
        cur_root->left = helper(preorder, inorder, pstart+1, pstart + pos_inorder - instart, instart, pos_inorder-1);
        cur_root->right = helper(preorder, inorder, pstart + pos_inorder - instart+1, pend, pos_inorder+1, inend);
        return cur_root;
    }
};
```
---
### [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)
> Given inorder and postorder traversal of a tree, construct the binary tree.
- 基本同上，注意下标
```c++
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        if (inorder.empty() || postorder.empty()){
            return NULL;
        }
        return helper(inorder, postorder, 0, inorder.size()-1, 0, postorder.size()-1);
    }
    
    TreeNode* helper(vector<int>& in, vector<int>& post, int instart, int inend, int pstart, int pend){
        if (instart > inend || pstart > pend){
            return NULL;
        }
        int cur_value = post[pend]; // 当前根节点值
        int pos = instart; // 根节点在中序遍历中的位置
        // 左子树节点个数 pos-instart   右子树节点个数 inend-pos
        for ( ; pos <= inend; pos++){
            if (in[pos] == cur_value){
                break;
            }
        }
        // 左右子树递归
        TreeNode* cur = new TreeNode(cur_value);
        // 此处注意下标
        cur->left = helper(in, post, instart, pos-1, pstart, pstart+pos-instart-1);
        cur->right = helper(in, post, pos+1, inend, pstart+pos-instart, pend-1);
        return cur;
    }
};
```

### [297. Serialize and Deserialize Binary Tree 二叉树序列化/反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)
> Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.
- 本质还是先序遍历增加对`None`指针的处理，反序列化时同理反推
```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Codec:
    
    def helper_encode(self, root, ans):
        if root is None:
            return ans + '#,'
        ans += str(root.val) + ','
        ans = self.helper_encode(root.left, ans)
        ans = self.helper_encode(root.right, ans)
        return ans

    def serialize(self, root):
        """Encodes a tree to a single string.
        
        :type root: TreeNode
        :rtype: str
        """
        return self.helper_encode(root, '')[:-1]
    
    def helper_decode(self, nodes):
        if nodes:
            if nodes[0] == '#':
                nodes.pop(0)
                return None
            root = TreeNode(nodes.pop(0))
            root.left = self.helper_decode(nodes)
            root.right = self.helper_decode(nodes)
            return root
        return None
    

    def deserialize(self, data):
        """Decodes your encoded data to tree.
        
        :type data: str
        :rtype: TreeNode
        """
        return self.helper_decode(data.split(','))
        

# Your Codec object will be instantiated and called as such:
# ser = Codec()
# deser = Codec()
# ans = deser.deserialize(ser.serialize(root))
```