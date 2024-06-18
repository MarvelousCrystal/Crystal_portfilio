---
layout: ../../layouts/post.astro
title: 完全二叉树的根节点
description: This is a post about how creating and developing a blog for yourself can be beneficial
dateFormatted: July 7th, 2023
---
@[TOC](由完全二叉树的节点个数引发的具体递归过程)

# 1. 题目描述
给你一棵 完全二叉树 的根节点 root ，求出该树的节点个数。

完全二叉树 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。

 

**示例 1：**
输入：root = [1,2,3,4,5,6]
输出：6

**示例 2：**
输入：root = []
输出：0

**示例 3：**
输入：root = [1]
输出：1

# 2. 常规方法
即将该二叉树当作普通二叉树，可进行递归遍历与层序遍历。
## 2.1 递归遍历

```cpp
//**递归遍历**
class Solution {
private:
    int getNodesNum(TreeNode* cur) {
        if (cur == NULL) return 0;
        int leftNum = getNodesNum(cur->left);      // 左
        int rightNum = getNodesNum(cur->right);    // 右
        int treeNum = leftNum + rightNum + 1;      // 中
        return treeNum;
    }
public:
    int countNodes(TreeNode* root) {
        return getNodesNum(root);
    }
};
```
## 2.2 层序遍历
```cpp
//**层序遍历**
int countNodes(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL) que.push(root);
        int result = 0;
        while (!que.empty()) {
            int size = que.size();
            for (int i = 0; i < size; i++) {
                TreeNode* node = que.front();
                que.pop();
                result++;   // 记录节点数量
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return result;
    }
```
## 2.3 复杂度
时间复杂度：O(n)
空间复杂度：O(n)

# 3. 优化后的递归
## 3.1 前置知识

 1. 首先需要明白什么是完全二叉树：==满二叉树==或者==只有最后一层叶子没有满==
 2. 对于==满二叉树==，可以直接用 2^树深度 - 1 来计算，注意这里根节点深度为1；
 	对于==只有最后一层叶子没有满==，分别递归左孩子，和右孩子，递归到某一深度一定会有左孩子或者右孩子为满二叉树，然后依然可以按照==满二叉树==来计算。> 具体例子见下述
 ![完全二叉树中的满二叉树](https://img-blog.csdnimg.cn/direct/a5fba214247141c8932aeffb5f28f306.png)
 ## 3.2 代码实现
 

```cpp
 int countNodes(TreeNode* root) {
        if(root == nullptr) return 0;
        TreeNode* left = root->left;
        TreeNode* right = root->right;
        int leftDepth = 0, rightDepth = 0;
        while(left){
            left = left->left;
            leftDepth++;
        }
        while(right){
            right = right->right;
            rightDepth++;
        }
        if(leftDepth == rightDepth) return (2<<leftDepth) - 1;
        int leftTree = countNodes(root->left);
        int rightTree = countNodes(root->right);
        int result = leftTree + rightTree + 1;
        return result;
    }
```
## 3.3 复杂度
时间复杂度：O(log n × log n)
空间复杂度：O(log n)
## 3.4 **递归全过程**
按上述题目所给示例1
![完全二叉树](https://img-blog.csdnimg.cn/direct/5e36f7a2796f408cbd4589b8a361bd8a.png)

 1. 从根节点的left和right开始，第一次执行递归函数**countNodes**,root就是根节点1，此时一直**root->left**,则**leftDepth**为2，**rightDepth**为1，两者不等，无法return, 将**root-val**为1的结点入栈；
 2. 运行到 **int leftTree = countNodes(root->left);** 此时root->val == 2，所以继续运行**countNodes**，发现 **leftDepth**== **rightDepth** =1，所以return 3并将leftTree=3压入栈；
 3. 继续执行下一个语句 **int rightTree = countNodes(root->right);** 此时栈内root->val为1,所以现在root-val为3，执行**countNodes**，发现**leftDepth**=1，**rightDepth** = 0，所以将root-val为3压入栈；
 4. 继续执行 **int leftTree = countNodes(root->left);** 此时root->val为6，**leftDepth**= **rightDepth** = 0，return 1并将leftTree=1压入栈，此时栈中root->val为3；
 5. 转而执行 **int rightTree = countNodes(root->right);** 发现return 0并将rightTree=1压入栈,然后执行 **int result = leftTree + rightTree + 1;** return result = 2,即rightTree = 2压入栈中，发现此时栈顶元素为root->val = 1,则 **return result = leftTree + rightTree + 1; ** 即为 3+2+1 = 6。此时递归结束。
 
 # 4 参考
链接: [代码随想录](https://www.programmercarl.com/0222.%E5%AE%8C%E5%85%A8%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E8%8A%82%E7%82%B9%E4%B8%AA%E6%95%B0.html)
链接: [leetcode222.完全二叉树的节点个数](https://leetcode.cn/problems/count-complete-tree-nodes/)
