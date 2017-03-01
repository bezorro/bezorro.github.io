---
layout: post
title:  "Leetcode: Single Number III"
categories: [leetcode]
comments: true
modified: 2017-03-01 19:31:03
---
## Question
>
>Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.
>
>For example:
>
>Given nums = [1, 2, 1, 3, 2, 5], return [3, 5]. 
<!--more-->

## Bit Manipulation
If the two `single numbers` are in two groups, each group contains one `single number` and other numbers exist twice. Then the XOR of one group is the `single number`. Now we are going to devide all numbers into two groups.  
Consider the XOR of all numbers in the given array `nums`, it is obvious that it is the XOR of two `single number`(a XOR b).  
Suppose that the lowbit(the lowest bit of an integer in binary system) of `a XOR b` is N. So a[N] and b[N] must be different. Now we can devide `nums` into two groups according to the Nth bit of the number.
{% highlight cpp %}
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int xsum = 0;
        for(auto p:nums) xsum ^= p;
        int lowbit = -xsum & xsum,ans=0;
        for(auto p:nums)
            if(p & lowbit) ans ^= p;
        return vector<int> {ans,xsum^ans};
    }
};
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)