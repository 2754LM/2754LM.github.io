---
title: 二叉树的对称dfs
tags:
- 算法
categories:
- 算法
- 杂谈
---

经典例题,对称二叉树

https://leetcode.cn/problems/symmetric-tree/description/

代码如图

```cpp
bool compare(TreeNode* left, TreeNode* right){
    if (left == NULL && right == NULL) return true;
    else if(left == NULL || right==NULL) return false;
    else if (left->val != right->val) return false;
    return compare(left->left, right->right) && compare(left->right, right->left);
 }

class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == NULL) return true;
        return compare(root->left, root->right);
    }
};
```

这题也可以,反转二叉树的奇数层

https://leetcode.cn/problems/reverse-odd-levels-of-binary-tree/description/

代码如下

```cpp
class Solution {
public:
    TreeNode* reverseOddLevels(TreeNode* root) {
        dfs(root->left, root->right, true);
        return root;
    }

    void dfs(TreeNode *l, TreeNode *r, bool jud) {
        if (l == nullptr) {
            return;
        }
        if (jud) {
            swap(l->val, r->val);
        }
        dfs(l->left, r->right, !jud);
        dfs(l->right, r->left, !jud);
    }
};

```

画个图

![](二叉树对称.png)

<!-- more -->
