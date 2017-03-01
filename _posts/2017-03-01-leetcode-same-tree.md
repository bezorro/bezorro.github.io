---
layout: post
title:  "Leetcode: Same Tree"
categories: [leetcode]
comments: true
modified: 2017-03-01 17:58:03
---
## Question

> Given two binary trees, write a function to check if they are equal or not.

>Two binary trees are considered equal if they are structurally identical and the nodes have the same value. 
<!--more-->

## Solution
{% highlight cpp %}
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(p == NULL && q == NULL) return true;
        if(p == NULL || q == NULL) return false;
        if (p->val != q->val) return false;
        return (isSameTree(p->left, q->left) && isSameTree(p->right, q->right));
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)