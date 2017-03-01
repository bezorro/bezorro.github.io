---
layout: post
title:  "Leetcode: Single Number II"
categories: [leetcode]
comments: true
modified: 2017-03-01 21:29:03
---
## Question

>Given an array of integers, every element appears three times except for one, which appears exactly once. Find that single one. 
<!--more-->

## Bit Manipulation
If XOR operation is the addition in binary system, which can solve `Single Number I`, we can use addition in ternary system to solve this problem.
H and L represents the higher and the lower bit of a ternary number.  
{% highlight cpp %}
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int L = 0, H = 0, tL, tH;
        for(auto P:nums)
            tH = (H & ~L & ~P) | (~H & L & P),
            tL = (~H & L & ~P) | (~H & ~L & P),
            H = tH,
            L = tL;
        return H | L;
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)