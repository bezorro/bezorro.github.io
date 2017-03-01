---
layout: post
title:  "Leetcode: Single Number"
categories: [leetcode]
comments: true
modified: 2017-03-01 17:43:03
---
## Question

>Given an array of integers, every element appears twice except for one. Find that single one.
<!--more-->

## XOR Bit Manipulation
Note that `a XOR a = 0`. Therefore, if the element exist twice, the XOR would be 0 so that only the single one remains. 
{% highlight c %}
int singleNumber(int* nums, int numsSize) {
    while(--numsSize)
        nums[0] ^=nums[numsSize];
    return nums[0];
}
{% endhighlight %}
- Time Complexity: O(n)
- Space Complexity: O(1)
